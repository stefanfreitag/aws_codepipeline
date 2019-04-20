<!-- markdownlint-disable MD012 MD014 -->

# AWS CodePipeline


* What capabilities does it offer?
* How does it work?
* Pros and cons compared to existing solution
  



## Where we would like to be

* Continuous deployment of applications
* Roll-out based on different strategies
* Easy roll-back
* Pipeline as Code


## AWS CodePipeline

* Continuous delivery service
* Helps to
  * model,
  * visualize, and
  * automate
the steps required to release our software


## AWS CodePipeline

helps to orchestrate(?)
* CodeCommit
  source code repository
* CodeBuild
  Continuous integration service
* CodeDeploy
  Automates application deployments


### Code Commit

* git-based source code repository
* HTTPS- and SSH-based access
* You must use a Git client that supports Git version 1.7.9 or later to connect to an AWS CodeCommit repository
* You must have an AWS CodeCommit managed policy attached to your IAM user,


### Code Build

* specify the location of your source code, choose your build settings, and CodeBuild will run build scripts for compiling, testing, and packaging your code. There are no servers to provision and scale, or software to install, configure, and operate. (Source: https://aws.amazon.com/codebuild/features/?nc=sn&loc=2)

* Supoprts different build environments for
  * Java OpenJDK 8, 9, 11
  * Python 3.6.5, 3.7.1
  * Node.js 8, 10
  * Ruby
  * Go 1.10, 1.11
  * Android
  * NET Core 2.1
  * Docker 17.09, 18.09
  
* Eine Build-Umgebung stellt eine Kombination aus Betriebssystem, Programmiersprachenlaufzeit und Tools dar, die CodeBuild zum Ausführen eines Builds verwendet. 

```shell
aws codebuild list-curated-environment-images
```

## CodeDeploy

* Automates application deployments to 
  * Amazon EC2 instances, 
  * on-premises instances, 
  * serverless Lambda functions, or 
  * Amazon ECS services.


* _Create application_
* Select as compute platform _Amazon ECS_
*  Create a deployment group
* TODO: Here we need to have already a cluster


## Scenarios in our team

Pipeline 1 (Triggered by code commit)

* Compile Code
* Run unit tests
* Run static code analysis
* Upload software artifact to repository

Pipeline 2a (Triggered by artifact upload)

* Use CodeDeploy to deploy artifact to environments (s)


Pipeline 2b (Triggered by artifact upload)

* Fetch artifact
* Build new Docker image
* Upload Docker image to registry
  


Pipeline 3 (Triggered by Docker image upload)

* Use CodeDeploy to deploy to ECS/ Fargate  
  

# CodePipel
# Pros and Cons

* Automated setup via CDK
* Integrates pretty well with other AWS services

## What we tried so far with TeamCity

* Increase control and automation
  * Move to own build servers
  * Automate the installation and configuration on the agents
  * Build configuration as code



# Demo

* Login to the [AWS console](https://aws.amazon.com/de/console/)


## Create a repository

* Go to _CodeCommit_ service
* Click _Create Repository_
* Provide repository name and optional description
* Click _Create_


### Connection information

* _Clone URL_
* _Clone HTTPS_


### Accessing the repository

* Switch to _IAM_ service and create a user
* Setup _HTTPS Git credentials for AWS CodeCommit_
* Save username and password
  
* Push code to repository

### Create the CodeBuild project

* Click _Create Project_
* Provide project name and optional description
* Select CodeCommit as Source provider
* Select repository name

Select Operating system, runtime and version
Specify S3 bucket for storing the artifact (create it before)



The  missing YAML file is a [build specification](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)


## Add a buildspec.yml to the git repository and commit

~~~~yaml
version: 0.2
phases:
  install:
    commands:
      - echo Entered the install phase...
  pre_build:
    commands:
      - echo Entered the pre_build phase...
    finally:
      - echo This always runs even if the login command fails
  build:
    commands:
      - echo Entered the build phase...
      - echo Build started on `date`
  post_build:
    commands:
      - echo Entered the post_build phase...
      - echo Build completed on `date`
~~~~

* Release change





### Creating a CodePipeline

* Go to _CodePipeline_ service
* Click _Create Pipeline_
* Enter the name of the new pipeline
* Select _AWS CodeCommit_ as source provider
* Select repository and branch  


* Add a build stage
* Select _AWS CodeBuild_ as build provider
* Click _Create Project_
  

* Specify project name (_NodeJsDemoVerify_ for first phase)
* Select Operating System, Runtime and Runtime version

~~~~sh
OS: Ubuntu
Runtime: Node.js
Runtime version: Node.js 8.11.0-1.6.0
~~~~



## Code Deploy

* Select _AWS Elastic Beanstalk_
* Select application name _NodeJsDemo_
* Select environment _NodeJsDemo-env_

* Watch the CodePipeline build for the first time


What happened?

~~~~sh
[Container] 2019/03/15 20:04:15 Waiting for agent ping
[Container] 2019/03/15 20:04:18 Waiting for DOWNLOAD_SOURCE
[Container] 2019/03/15 20:04:18 Phase is DOWNLOAD_SOURCE
[Container] 2019/03/15 20:04:18 CODEBUILD_SRC_DIR=/codebuild/output/src416057865/src
[Container] 2019/03/15 20:04:18 Phase complete: DOWNLOAD_SOURCE Success: false
[Container] 2019/03/15 20:04:18 Phase context status code: YAML_FILE_ERROR Message: YAML file does not exist
~~~~



## Further information

* [CodePipeline](https://aws.amazon.com/codepipeline/)
* [CodeCommit](https://aws.amazon.com/de/codecommit/)
* [CodeBuild](https://aws.amazon.com/de/codebuild/)
* [CodeDeploy](https://aws.amazon.com/de/codedeploy/)
* [ElasticBeanstalk](https://aws.amazon.com/de/elasticbeanstalk/)
* [NodeJS Demo](https://docs.aws.amazon.com/de_de/elasticbeanstalk/latest/dg/RelatedResources.html)


* [CodeBuild Provided Images](https://docs.aws.amazon.com/de_de/codebuild/latest/userguide/build-env-ref-available.html)