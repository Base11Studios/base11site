---
date: 2018-03-17
title: Painfully Simple AWS Serverless Apps
categories:
  - aws
  - codestar
  - serverless
  - codepipeline
  - codebuild
  - cloudformation
  - lambda
  - apigateway
  - s3
  - iam
author_staff_member: ryan
featured_image: aws/aws-codestar.png
image:
  path: /images/aws/aws-codestar.png
---

Cloud computing can be intimidating, but there are tools to make your life easier. One of those tools is [AWS CodeStar](https://aws.amazon.com/codestar/). Among other stacks, it allows you to quickly deploy a [Serverless App](https://aws.amazon.com/serverless/?nc1=f_dr). The AWS documentation is great, but there is **a lot** of it. In this post I'll try to bubble up some of the more important points and share some of the things I've learned.

## Getting Started

The first thing you'll need to do is [create an AWS account](https://portal.aws.amazon.com/billing/signup#/start). Unless you start having heavy traffic, your cost will be minimal, if not [zero](https://aws.amazon.com/free/). Once you're logged into the console, type `CodeStar` into the services search. Like I said, the [docs](https://docs.aws.amazon.com/codestar/latest/userguide/setting-up.html) are great, but here's the tl;dr;

1. Click `+ Create a new project`
2. Since we're going serverless, filter by `Web service` and `AWS Lambda`
3. Pick the language of your choice. I prefer Python or JS. To me, Java feels a little heavy for a serverless app
4. Name your project and choose your source control. I prefer GitHub, because it's GitHub
5. You can set up a specific IDE, but it's not required. Since your code is in GitHub, you can just check it out and edit your code however you please
6. The next screen you see will be your project home page. You'll see two modules. **Application endpoints** and **Continuous Deployment**. Wait until things stop spinning, then click the link in the Application endpoints module

## Did I just build a RESTful API in 6 steps?

Yes! Yes you did, but what the heck happened? AWS is just a big set of products that help you build out your infrastructure. It's flexible and scalable. CodeStar creates two CloudFormation templates to set up these products for you. In short, [CloudFormation](https://aws.amazon.com/cloudformation/) is infrastructure as code. Templates written in JSON or YAML are parsed by CloudFormation to automatically build your cloud infrastructure. One template builds all the support products, such as your deploy pipeline. The other is your actual service which includes your code and API. I'll go over CloudFormation in more detail in another post.

## The Support Products

In this section I'll break down each of the support components of your Serverless Architecture.

### CodePipeline

[CodePipeline](https://aws.amazon.com/codepipeline/) is... a code pipeline. It lets you string together code, build, and deploy for continuous delivery and integration. Just as easily as CodeStar built your app, it built your pipeline. You get CD and CI just like that! Your code is pulled from GitHub every time you push to master. Build is handled by CodeBuild. The app is deployed through CloudFormation. You can see your pipeline on your CodeStar project page or by opening the CodePipeline console.

### CodeBuild

The build step of your pipeline is handled by [CodeBuild](https://aws.amazon.com/codebuild/). When CodeStar built the pipeline, it also created an S3 bucket. The source step of the pipeline clones your repo from GitHub, zips it up, and sticks it in the S3 bucket. It is then sent to CodeBuild as an input. The files are unzipped and provided to you as a working directory. The directory needs a file named `buildspec.yml` in the root. Here's an example that is similar to the one you have in your sample project.

```yaml
version: 0.2

phases:
  install:
    commands:

      # Upgrade AWS CLI to the latest version
      - pip install --upgrade awscli

  pre_build:
    commands:

      # Discover and run unit tests in the 'tests' directory. For more information, see <https://docs.python.org/3/library/unittest.html#test-discovery>
      - cd aws
      - python -m unittest discover tests
  
  build:
    commands:
      # Use AWS SAM to package the application by using AWS CloudFormation
      - aws cloudformation package --template template.yml --s3-bucket $S3_BUCKET --output-template template-export.yml

artifacts:
  type: zip
  files:
    - aws/template-export.yml
```

> The sample project contains all your files in the root. In my projects I've had a need for other files. I usually nest everything in an `/aws` folder which is why you see `cd aws`. If you choose to do this, you'll need to update your CodeStar template to look for `aws/template-export.yaml`. After updating the template, deploy the template again and your CodePipeline will be updated. As I mentioned, I'll discuss templates in a separate post.

The key step in your build is [aws cloudformation package](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/package.html). It generates the output of the build step and sticks it in your S3 bucket. The last step of your build takes the template as input and deploys it using CloudFormation.

### S3

[Super Simple Storage(S3)](https://aws.amazon.com/s3/) is the AWS version of DropBox. You can drop anything from web assets to encryption keys in your S3 buckets. As mentioned, build assets are stored in S3 for the build process.

### IAM

If there is one product you want to take a deeper dive on after reading this post, it's [IAM](https://aws.amazon.com/iam/). It stands for Identity and Access Management and it has an impact on everything you build in AWS. When you were setting up CodeStar there was a check box that said `AWS CodeStar would like permission to administer AWS resources on your behalf.` This granted CodeStar permission, through IAM, to manage AWS resources. CodeStar generated a policy, that was provided to your pipeline to give it access to your S3 buckets. There's also a policy on the S3 bucket itself that prevents outside users from getting to your build assets. Again, IAM is critical to everything you do in AWS. Read up!

## The Serverless App

The second template CodeStar generates builds out the components of your app. Here's the breakdown.

### Lambda

[Lambda](https://aws.amazon.com/lambda/) is the heart of the Serverless Architecture. You write your code, it does the rest. No need to provision servers or configure an app container. Lambda functions can be triggered by all sorts of AWS events, but the function in your sample is triggered by the API Gateway. `template.yml` defines both your Lambda function and how it reacts to your API.

```yaml
AWSTemplateFormatVersion: 2010-09-09
Transform:
- AWS::Serverless-2016-10-31
- AWS::CodeStar

Parameters:
  ProjectId:
    Type: String
    Description: CodeStar projectId used to associate new resources to team members

Resources:
  HelloWorld:
    Type: AWS::Serverless::Function
    Properties:
      Handler: index.handler
      Runtime: python3.6
      Role:
        Fn::ImportValue:
          !Join ['-', [!Ref 'ProjectId', !Ref 'AWS::Region', 'LambdaTrustRole']]
      Events:
        GetEvent:
          Type: Api
          Properties:
            Path: /
            Method: get
        PostEvent:
          Type: Api
          Properties:
            Path: /
            Method: post
```

### API Gateway

The [API Gateway](https://aws.amazon.com/api-gateway/) is the abstraction between your code and the rest of the world. You set up endpoints that can receive HTTP requests, authenticate them, transform the input to a Lambda function, transform the output of the Lambda function, and respond to the client. You can also handle errors and mock out responses. You can use a swagger template to further define your API and then generate code for clients. Any time you can put your configuration in code, like a swagger file, do it.

### Now What

Code! As I mentioned, the beauty of CodeStar is it gets you right to the fun part, writing code. Try adding another Lambda function and endpoint. Once you've got that down check out [DynamoDB](https://aws.amazon.com/dynamodb/) for some structureless persistance. I barely touched the surface of each component here. I'll take a deeper dive on some of them in later posts. As I said, the AWS docs are great so get to reading and building!

[Tweet](https://twitter.com/rootbur) or [email](mailto:ryan@base11studios.com) me with questions or feedback on this post.
