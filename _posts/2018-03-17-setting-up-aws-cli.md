---
date: 2018-03-17
title: Quickly Setup AWS CLI on macOS
categories:
  - aws
  - cli
  - python3
  - macos
  - pip3
author_staff_member: ryan
featured_image: octocats/inspectocat.jpg
image:
  path: /images/octocats/inspectocat.jpg
---

⚠️[There's a better way to do this!]({{site.baseur}}{% link _posts/2019-01-21-aws-cli-with-pyenv.md%})⚠️

The doc for [installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-install-macos.html), if you don't have pip and choose to use python3, has one little trick to it.

## Installing AWS CLI with python3 on macOS

1. Download the latest version of [python](https://www.python.org/)
2. Run the package installer and delete it when finished
3. Install pip by running these commands. The `--user` is important
    ```bash
    curl -O https://bootstrap.pypa.io/get-pip.py
    python3 get-pip.py --user
    ```
4. Install the aws cli. Again, `--user` is important
    ```bash
    pip3 install awscli --upgrade --user
    ```
5. Step 4 mentioned in the aws doc says to verify the cli is installed by running `aws --version`. Run the command, if it fails, continue reading. If it doesn't, you're good to go
6. We need to get the cli added to your path. This is where the trick comes in. The doc suggests the aws bin is at `~/Library/Python/3.6/bin`, which it is. However, the doc goes on to say
    > pip installs executables to the same folder that contains the Python executable. Add this folder to your PATH variable.

    which is not true. It's installed at `~/Library/Python/3.6/bin`. The pip [docs](https://pip.pypa.io/en/stable/user_guide/#user-installs) say

    > The default location for each OS is explained in the python documentation for the site.USER_BASE variable.

    [site.USER_BASE](https://docs.python.org/3/library/site.html#site.USER_BASE) turns out to be `~/Library/Python/X.Y`. That's why `--user` is important. You're installing into the user space.
7. So, you'll want to add `~/Library/Python/3.6/bin` to your path to run the aws bin

Some of you may just see the location aws suggests and run with it. But it annoyed me that it wasn't clear why they called out that location. I found satisfaction in getting to the bottom of this and maybe it will help someone else.