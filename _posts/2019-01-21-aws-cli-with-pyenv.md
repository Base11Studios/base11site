---
date: 2019-01-21
title: Setting up the AWS CLI with pyenv
categories:
  - aws
  - cli
  - python3
  - macos
  - pip3
  - pyenv
author_staff_member: ryan
featured_image: octocats/inspectocat.jpg
image:
  path: /images/octocats/inspectocat.jpg
---

I wrote an earlier [post]({{site.baseur}}{% link _posts/2018-03-17-setting-up-aws-cli.md%}) on setting up the AWS CLI on macOS. After almost a year of learning AWS and Python, I discovered there is a much better way. The main driver here is avoiding impact to the Apple Python installation.

## Use pyenv for Python Version Management

There are many versions of Python. Different providers, such as AWS, support specific versions or use specific versions in their tooling. Also, some Python packages only work on certain versions.

[pyenv](https://github.com/pyenv/pyenv) helps us manage those versions in isolation from the global installation. I suggest installing [Homebrew](https://brew.sh/) and then following the [pyevn Homebrew installation](https://github.com/pyenv/pyenv#homebrew-on-macos) steps. Be sure to set up the [suggested build environment](https://github.com/pyenv/pyenv/wiki#suggested-build-environment).

> From the AWS docs, Python 2 version 2.6.5+ or Python 3 version 3.3+ is required

## Python Virtual Environments

For similar reasons mentioned above, we need to be able to create Python environments that mimic our deployment environments. We may also want to tinker and be able to blow away an environment. We use virtual environments for this.

I favor [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv). Again, follow the [Homebrew steps](https://github.com/pyenv/pyenv-virtualenv). Once installed, follow [usage steps](https://github.com/pyenv/pyenv-virtualenv#usage).

The [activate virtualenv](https://github.com/pyenv/pyenv-virtualenv#activate-virtualenv) section has a useful tidbit. If you add `eval "$(pyenv virtualenv-init -)"` to your shell, you can cd into your project directory and run `pyenv local <project name>`. Now, when you cd into the project directory, you'll be in  your virtual environment!

## Install the CLI

This part is simple. cd into your project directory, assuming you have auto activation setup, run `pip install --upgrade awscli`. Pip was installed for us by pyenv-virtualenv so we're good to go.

You can now work through some of the [configuration steps](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html).

## Clean-up

Part of the beauty here is we can easily blow away what we've done. Delete the `.python-version` file in your project directory then run `pyenv uninstall <virtual environment name>`. And if you don't want the Python version anymore you can run `pyenv uninstall <python version>`. Then run `pyenv versions` to confirm.