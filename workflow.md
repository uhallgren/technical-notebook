# Github Workflows


## Workflow that uses private image for job

```
# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "main" branch
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

env:
  TAG: 1.0.3

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:


  # This workflow contains a single job called "build"
  login-to-amazon-ecr:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1-node16
        with:
          aws-access-key-id: ${{ secrets.CONNECTED_HOME_DEV_ACCESS_KEY }}
          aws-secret-access-key: ${{ secrets.CONNECTED_HOME_DEV_SECRET_KEY }}
          aws-session-token: ${{ secrets.CONNECTED_HOME_DEV_SESSION_TOKEN }}
          aws-region: eu-west-1
          mask-aws-account-id: no

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1

      - name: Build, tag, and push docker image to Amazon ECR
        env:
          REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          REPOSITORY: ulf/test1
        run: |
          docker build -t $REGISTRY/$REPOSITORY:$TAG .
          docker push $REGISTRY/$REPOSITORY:$TAG

    outputs:
      tag: ${{ env.TAG }}
      registry: ${{ steps.login-ecr.outputs.registry }}
      docker_username: ${{ steps.login-ecr.outputs.docker_username_642976147714_dkr_ecr_eu_west_1_amazonaws_com }} 
      docker_password: ${{ steps.login-ecr.outputs.docker_password_642976147714_dkr_ecr_eu_west_1_amazonaws_com }}

  build-docker-image:
    name: Run example in container
    needs: login-to-amazon-ecr
    runs-on: ubuntu-latest
    container:
      image: '${{ needs.login-to-amazon-ecr.outputs.registry }}/ulf/test1:${{ needs.login-to-amazon-ecr.outputs.tag }}'
      credentials:
        username: ${{ needs.login-to-amazon-ecr.outputs.docker_username }}
        password: ${{ needs.login-to-amazon-ecr.outputs.docker_password }}

    steps:
     # Runs a single command using the runners shell
      - name: Run a one-line script
        run: echo Hello, world! ${{ env.TAG }}

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.
          echo using tag $TAG
          cat /message

```# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the "main" branch
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

env:
  TAG: 1.0.3

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:


  # This workflow contains a single job called "build"
  login-to-amazon-ecr:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1-node16
        with:
          aws-access-key-id: ${{ secrets.CONNECTED_HOME_DEV_ACCESS_KEY }}
          aws-secret-access-key: ${{ secrets.CONNECTED_HOME_DEV_SECRET_KEY }}
          aws-session-token: ${{ secrets.CONNECTED_HOME_DEV_SESSION_TOKEN }}
          aws-region: eu-west-1
          mask-aws-account-id: no

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1

      - name: Build, tag, and push docker image to Amazon ECR
        env:
          REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          REPOSITORY: ulf/test1
        run: |
          docker build -t $REGISTRY/$REPOSITORY:$TAG .
          docker push $REGISTRY/$REPOSITORY:$TAG

    outputs:
      tag: ${{ env.TAG }}
      registry: ${{ steps.login-ecr.outputs.registry }}
      docker_username: ${{ steps.login-ecr.outputs.docker_username_642976147714_dkr_ecr_eu_west_1_amazonaws_com }} 
      docker_password: ${{ steps.login-ecr.outputs.docker_password_642976147714_dkr_ecr_eu_west_1_amazonaws_com }}

  build-docker-image:
    name: Run example in container
    needs: login-to-amazon-ecr
    runs-on: ubuntu-latest
    container:
      image: '${{ needs.login-to-amazon-ecr.outputs.registry }}/ulf/test1:${{ needs.login-to-amazon-ecr.outputs.tag }}'
      credentials:
        username: ${{ needs.login-to-amazon-ecr.outputs.docker_username }}
        password: ${{ needs.login-to-amazon-ecr.outputs.docker_password }}

    steps:
     # Runs a single command using the runners shell
      - name: Run a one-line script
        run: echo Hello, world! ${{ env.TAG }}

      # Runs a set of commands using the runners shell
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.
          echo using tag $TAG
          cat /message