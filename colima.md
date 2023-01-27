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

The configuration file, *~/.colima/default/colima.yaml*, will be opened with vi when the *--edit* option is given to the start command. The recommended changes are given below:

    cpu: 6
    disk: 60
    memory: 6
    forwardAgent: true
    vmType: vz
    mountType: virtiofs

This gives better performance than using the *qemu*. The forwarding of the *ssh agent* makes it possible to perform *ssh* connections from the container. To take advantage of this you need to add your private key to the *ssh agent*. Add the following to your *.zprofile*.

    # Add private key to ssh-agent.
    # This is needed for Rust git references
    ssh-add ~/.ssh/id_rsa

It should now be possible to use *ssh* with the same identity as from the MacBook.
To perform the above changes to the configuration start as below the first time.

    colima start --edit 

    docker compose versions
    docker run hello-world

## Check that it is possible to use with ECR

    aws --profile dev sso login
    ecr_login 
    docker pull $registry/hc-devcon-cpp-arm64:1.0.13 

    



