---
date: 2018-03-18
title: Painfully Simple Infrastructure as Code
categories:
  - aws
  - cli
  - s3
  - cloudformation
author_staff_member: ryan
featured_image: aws/cloudformation.png
image:
  path: /images/aws/cloudformation.jpg
---

Infrastructure in source control?! You bet. Cloud providers, such as [AWS](https://aws.amazon.com/?nc2=h_lg), have made [Infrastructure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_Code) simple with tools like [CloudFormation](https://aws.amazon.com/cloudformation/). With our app code we have tools to track history, run tests, automate deployments, execute rollbacks, and much more. This post will walk through a simple use case to demonstrate how we can apply some of those same concepts to infrastructure.


## What we're gonna do

Recently, I had the need for an S3 bucket to host web assets. I had already set up an [AWS Serverless App with CodeStar]({{ site.baseurl }}{% link _posts/2018-03-17-aws-introduction.md%}) which makes use of IaC. I didn't want to manage the rest of my assets through the GUI and wanted all the benefits I got using CodeStar. We'll do some one time setup in the GUI and occasionally reference it, but we'll mostly be using the command line to deploy an S3 bucket. This post is all about CloudFormation and IaC. [IAM](https://aws.amazon.com/iam/), the [AWS CLI](https://aws.amazon.com/cli/), [CodeStar](https://aws.amazon.com/codestar/), and a few other AWS products will be mentioned but not discussed in detail.

## Create the Initial Template

Templates are the code in our Infrastructure as Code. Executing a template creates a stack, which is our actual infrastructure.

Log into your AWS account and open CloudFormation. Click `Create Stack` and then `Design Template`. You can use the GUI to drag and drop elements, but I prefer to use one of the [snippets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/CHAP_TemplateQuickRef.html) from the docs. I've said it before, the AWS docs are awesome, and wouldn't you know they have a snippet for exactly what we want! Here's the snippet for an S3 bucket for Website Hosting. I like YAML. You don't need as many structure characters such as {}, "", and [].

```yml
AWSTemplateFormatVersion: 2010-09-09
Resources:
  S3Bucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      AccessControl: PublicRead
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html
    DeletionPolicy: Retain
  BucketPolicy:
    Type: 'AWS::S3::BucketPolicy'
    Properties:
      PolicyDocument:
        Id: MyPolicy
        Version: 2012-10-17
        Statement:
          - Sid: PublicReadForGetBucketObjects
            Effect: Allow
            Principal: '*'
            Action: 's3:GetObject'
            Resource: !Join 
              - ''
              - - 'arn:aws:s3:::'
                - !Ref S3Bucket
                - /*
      Bucket: !Ref S3Bucket
Outputs:
  WebsiteURL:
    Value: !GetAtt 
      - S3Bucket
      - WebsiteURL
    Description: URL for website hosted on S3
  S3BucketSecureURL:
    Value: !Join 
      - ''
      - - 'https://'
        - !GetAtt 
          - S3Bucket
          - DomainName
    Description: Name of S3 bucket to hold website content
```

In the lower editor in the designer, select the template tab, switch to YAML, and paste the template.

The template is descriptive, detailed, and easy to read. Let's break it down a little bit.

### Resources

Resources are your AWS assets. I have yet to come across an AWS product I couldn't build in a CloudFormation template. We're creating an S3Bucket and BucketPolicy. The `Bucket: !Ref S3Bucket` makes it easy for us to automatically apply the bucket policy to our new bucket. 

### Outputs

[Outputs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html) are values returned by the execution of the template. You can view the when you describe the stack, in the console, or export them for use in other stacks. This well be useful later.

I encourage you to investigate some of the other properties mentioned in the template. You have a lot of control and power in customizing these templates. An important one is the [Deletion Policy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-attribute-deletionpolicy.html). In a dev environment you may want to blow away all your resources in a stack to keep things clean. In prod, you may want a little extra protection and not want to delete everything when you blow away your stack. 

## Validate the Template. Deploy the Stack

Back in the designer click the `Validate Template` button which is a checkbox icon in the top left corner. After validating, click the cloud icon in the top left corner to deploy your stack. You'll be redirected to a workflow for creating your stack. The default values should suffice except for the IAM Role. I'd recommend creating a Role with a policy that has permissions to manage the resources in your stack. Finish the workflow and click create.

At this point, your template is being parsed and the AWS resources are being created. Back on the stacks screen, you can click on the stack name and see events as the stack is created. When it's done, you can navigate to the S3 console and see your created bucket.

Go back to the details of your stack, open the template, copy it, and paste it into a git repo. You now have Infrastructure as Code. **Hello, World!**

## Modify the Template. Update the Stack

Now that we have a bucket we need to get our content into it. ~~Just open a browser, open your bucket, and manually upload the files~~. To avoid manually having to upload our files *every time* we change them, we'll utilize the continuous integration/deployment provided by CodePipeline and CodeBuild. I'll go over that set up in another post. What we'll focus on here is adding an IAM Role to our stack and exporting it so it can be used by our CodeStar stack. You should now have the template available locally and in source control. We'll modify it then use the AWS CLI to validate it, create a change set, and ultimately change our stack.

Here's the updated template with our new IAM role ready for export

```yaml
AWSTemplateFormatVersion: 2010-09-09
Resources:
  AngularHostBucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      AccessControl: PublicRead
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html
    DeletionPolicy: Retain
  BucketPolicy:
    Type: 'AWS::S3::BucketPolicy'
    Properties:
      PolicyDocument:
        Id: MyPolicy
        Version: 2012-10-17
        Statement:
          - Sid: PublicReadForGetBucketObjects
            Effect: Allow
            Principal: '*'
            Action: 's3:GetObject'
            Resource: !Join 
              - ''
              - - 'arn:aws:s3:::'
                - !Ref AngularHostBucket
                - /*
      Bucket: !Ref AngularHostBucket
  AngularHostBucketRole:
    Type: 'AWS::IAM::Role'
    Description: Allow codebuild to write angular app to S3 bucket
    Properties:
      AssumeRolePolicyDocument:
        Statement:
          - Action: 'sts:AssumeRole'
            Effect: Allow
            Principal:
              Service: codebuild.amazonaws.com
      Path: /      
  S3BuildPolicy:
    Type: 'AWS::IAM::Policy'
    Description: Allow codebuild to write angular app to S3 bucket
    Properties:
      PolicyName: s3buildpolicy
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Action:
            - s3:*
          Resource: !Join 
              - ''
              - - 'arn:aws:s3:::'
                - !Ref AngularHostBucket
                - /*
      Roles: 
        - !Ref AngularHostBucketRole
Outputs:
  WebsiteURL:
    Value: !GetAtt 
      - AngularHostBucket
      - WebsiteURL
    Description: URL for website hosted on S3
  S3BucketSecureURL:
    Value: !Join 
      - ''
      - - 'https://'
        - !GetAtt 
          - AngularHostBucket
          - DomainName
    Description: Name of S3 bucket to hold website content
  AngularHostBucketRole:
    Description: Role to allow build process access to the bucket that hosts the angular app
    Value: !Ref AngularHostBucketRole
    Export:
      Name: AngularHostBucketRole
```

There's a few changes to the resource names to be less generic, but most importantly, I created the IAM policy and added it to an IAM role. At the bottom, it gets exported for use by other stacks.

### Validate Template

> You'll need to set up the [AWS CLI](https://aws.amazon.com/cli/) to move forward. ({{ site.baseurl }}{% link _posts/2018-03-17-setting-up-aws-cli.md%}) may also be useful to you.

```bash
aws cloudformation validate-template --template-body file://angular-host-bucket-stack.yml
```

> The `file://` is required on macs. It's weird, but it is.

### Create Change Set

This gives you a preview of what changes will be when you actually trigger the update

```bash
aws cloudformation create-change-set --stack-name angular-host-bucket-stack --template-body file://angular-host-bucket-stack.yml --change-set-name update-permissions --capabilities "CAPABILITY_IAM"
```

For this part, I like the GUI. Go back to the CloudFormation console and open your stack. Scroll to the bottom and expand `Change Sets`. You should see that we are adding a Role and modifying a Policy.

### Execute Change Set

```bash
aws cloudformation execute-change-set --change-set-name update-permissions --stack-name angular-host-bucket-stack
```

### Confirmation

If you open your browser back up, the stack will have a status of `UPDATE_IN_PROGRESS` and you can watch the `Events` section for changes as they happen. In `Outputs` you'll see our newly exported role for use in other stacks.

If your `execute-change-set` command failed for some reason, you'll need to re-create the change set.

## Conclusion

Once you're done modifying your template, check it in to source control. Now you have a history of your changes and can easily roll back. You could even set up a new CodePipeline to automate the deployment of your stacks just like CodeStar. IaC is amazing!