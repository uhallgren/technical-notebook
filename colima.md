# Colima

The current setup using *Docker Desktop* has several issues;

* Files created in the docker container on bind mount strange mod flags
* The Docker Desktop seems to consume all CPU and hang the machine 
* The license model requires large companies to have a paid license.

An alternative to using *Docker Desktop* is to try to use [Colima](https://github.com/abiosoft/colima).

## Uninstall Docker Desktop

**Note:** All local images, containers, volumes, etc. will be removed.

This step may require admin privileges.

    brew remove --cask docker

Remove the current docker configuration files.

    rm -ri ~/.docker

## Install Colima

Install Colima using brew.

    brew install Colima
    brew install docker
    
    mkdir -p ~/.docker/cli-plugins
    curl -SL https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-darwin-aarch64 \
         -o ~/.docker/cli-plugins/docker-compose
    chmod +x ~/.docker/cli-plugins/docker-compose

## Configure and start 

    colima start --edit 

    docker compose versions
    docker run hello-world

## Check that it is possible to use with ECR

    aws --profile dev sso login
    ecr_login 
    docker pull $registry/hc-devcon-cpp-arm64:1.0.13 

    



