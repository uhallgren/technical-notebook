# Elastic Container Registry

This document explains how the Amazon Web Service Elastic Container Registry (AWS ECR) can be used for **Docker Images**. This is intended to be used as a replacement for the **Docker Hub** *connectedhome* account. The current **Docker Hub** account has a limited number of seats and is for non-profit organizations.

# Setup AWS for command line usage

It is assumed that you already have requested access to the AWS instances and can login to [Amazon Web Services (AWS)](https://teliacompany.awsapps.com/start#/). This explains how to use AWS from the command line, which is needed to use **docker** commands.

Install the AWS CLI client.

```console 
brew install awscli
```

It is possible to configure *AWS CLI client* the hardway, as explained in some telia documentation, by using the 
```console
aws sso configure
```
command

The easy way, which is explained below, is to create the configuration file directly. This can be done by creating the **~/.aws/config** file manually.
```console 
[profile dev]
sso_start_url = https://teliacompany.awsapps.com/start
sso_region = eu-west-1
sso_account_id = 642976147714
sso_role_name = Administrator
region = eu-west-1
output = json
[profile stage]
sso_start_url = https://teliacompany.awsapps.com/start
sso_region = eu-west-1
sso_account_id = 998135273239
sso_role_name = Administrator
region = eu-west-1
output = json
[profile prod]
sso_start_url = https://teliacompany.awsapps.com/start
sso_region = eu-west-1
sso_account_id = 883291977974
sso_role_name = Administrator
region = eu-west-1
output = json
```

This file defines three different profiles, each one for a specific AWS instance. These names for the profiles (dev, stage, prod) are used in the following examples but could of course be different.

## Verify AWS CLI configuration

Login to AWS using the *aws* command.
```console
% aws --profile dev sso login
```
A web window should be opened and you may enter your credentials the same way as when authenticating for the web user interface. It is possible to verify the login using the *AWS Security Token Service*.
```console
 % aws --profile dev sts get-caller-identity
{
    "UserId": "AROALISNOTREAL:sluggo.developson@teliacompany.com",
    "Account": "642976147714",
    "Arn": "arn:aws:sts::642976147714:assumed-role/AWSReservedSSO_Administrator_ff41bd1234562481/sluggo.developson@teliacompany.com"
}
prl167@DY4MH92QD1 ~ % 
```
You may wish to do the same for the other profiles as well, to verify that they are indeed working.

## Avoid giving profile option

It is possible to avoid using the "--profile dev" option for all commands. If you are working with the dev environment you may set an environment variable:

```shell
export AWS_PROFILE=dev
```

# Configure the shell

The *registry* name is quite long and hard to remember by heart, so it is better to use environment variables and some alias. Add the following 
to your *.zprofile* file.
```shell
# Using AWS ECR as a docker registry (connected-home-dev)
export  registry="642976147714.dkr.ecr.eu-west-1.amazonaws.com"
alias ecr_login='aws ecr get-login-password --profile dev | \
    && docker login --username AWS --password-stdin $registry'
alias ecr_ls='aws --profile dev ecr describe-repositories --query "repositories[*].repositoryName"'
alias ecr_ls_tags="aws --profile dev ecr list-images  --query 'imageIds[?imageTag!=``null``] | sort_by(@, &imageTag)[].imageTag' --repository-name"
alias ecr_create='aws --profile dev ecr create-repository --repository-name'
alias ecr_rm='aws --profile dev ecr delete-repository --repository-name'
alias ecr_rm_tags=' aws --profile dev ecr batch-delete-image --repository-name'
```

# Usage

It is important to remember that the setup works in a similar way to **Docker Hub**. It is needed to go to the website to create repositories and to remove them. This is not something that you may do with the *docker* command. The difference is that the registry URL is more complicated than the one used for *Docker Hub**.

You may go to [Amazon Elastic Container Registry](https://eu-west-1.console.aws.amazon.com/ecr/repositories?region=eu-west-1) and perform the same type of operations as you would for [docker hub](https://hub.docker.com/orgs/connectedhome/repositories)

The *AWS ECR* is defined in the *development* instance in the region *eu-west-1*.

The **Docker Hub** version would be
```console
connectedhome/hc-devcon-cpp-arm64:1.0.12
```

```console
642976147714.dkr.ecr.eu-west-1.amazonaws.com/hc-devcon-cpp-arm64:1.0.12
```

The terminology used by *docker* is not consistent but you could still refer to the parts as

| Part in the URL                                   | Description                        |
|---------------------------------------------------|------------------------------------|
| 642976147714.dkr.ecr.eu-west-1.amazonaws.com      | Registry for ECR                   |
| connectedhome                                     | Registry for Docker Humb           |
| hc-devcon-cpp-arm64                               | The image name                     |
| 1.0.12                                            | The version tag                    |

To pull an image you need first to authenticate with AWS (if not already authenticated).
```shell
% aws --profile dev sso login
```
The next step is to authenticate with the *docker login* command.
```shell
aws ecr get-login-password --profile dev | docker login --username AWS --password-stdin 642976147714.dkr.ecr.eu-west-1.amazonaws.com
```
*Just kidding*, of course, there is an easier way, do you remember the alias put into the *.zprofile* earlier.
```shell
 % ecr_login
Login Succeeded

Logging in with your password grants your terminal complete access to your account. 
For better security, log in with a limited-privilege personal access token. Learn more at https://docs.docker.com/go/access-tokens/
```

To pull an image you may e.g. do the following:
```shell
% docker pull $registry/hc-devcon-cpp-arm64:1.0.12
1.0.12: Pulling from hc-devcon-cpp-arm64
Digest: sha256:173416b4350dbfdc92de39e051f68572be4722cc5ed46ec60acb91af369a280a
Status: Image is up to date for 642976147714.dkr.ecr.eu-west-1.amazonaws.com/hc-devcon-cpp-arm64:1.0.12
642976147714.dkr.ecr.eu-west-1.amazonaws.com/hc-devcon-cpp-arm64:1.0.12
```

The different aliases provide some more utilities.

## List the images
```shell
% ecr_ls
[
    "hc-devcon-cpp-arm64",
    "lcm-backend",
    "nats",
    "mqtt-broker",
    "grafana/grafana",
    "hc-devcon-cpp-amd64"
]
```

## Create a repository
```shell
% ecr_create kalle

 % ecr_ls
[
    "hc-devcon-cpp-arm64",
    "lcm-backend",
    "nats",
    "mqtt-broker",
    "grafana/grafana",
    "kalle",
    "hc-devcon-cpp-amd64"
]
```

# Remove a repository
```shell
% ecr_rm kalle
```

# Remove a tag from a repository
```shell
 % ecr_ls_tags --repository-name hc-devcon-cpp-arm64
[
    "1.0.12",
    "1.0.1",
    "1.0.11",
    "1.0.10",
    "1.0.0"
]

 % ecr_rm_tag --repository-name hc-devcon-cpp-arm64 --image-ids imageTag=1.0.0
```

