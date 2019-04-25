<!-- markdownlint-disable MD012 MD014 -->

# AWS CodePipeline



## Where are we now

* Teamcity
  * agents setup via Chef
  * Build projects managed via  UI
  * Tested TC Pipeline as Code
* Microservices
  * Self-contained jars
  * Deployed to EC2 instances
  * Chef


## What we are looking for

* More automation
* Continuous deployment
  * Orchestration layer
  * Roll-out strategies
  * Easy Rollback


## AWS CodePipeline

* Continuous delivery service
* Model, visualize, and automate the steps required to release our software

<img src="images/PipelineFlow.png" height="450px"/>


### Components

* CodeCommit
  
  source code repository
* CodeBuild
  
  Continuous integration service
* CodeDeploy
  
  Automates application deployments


### Code Commit

* git-based source code repository
* HTTPS- and SSH-based access

<img src="images/codecommit_screenshot.png" height="450px"/>


### Code Build

* Runs build scripts for compiling, testing, and packaging
* No servers to provision and scale, or software to install, configure, and operate.
* Supoprts different build environments for
  * Java OpenJDK 8, 9, 11
  * NET Core 2.1
  * Python 3.6.5, 3.7.1
  * Node.js 8, 10
  * Docker 17.09, 18.09
  * ...
  
```shell
aws codebuild list-curated-environment-images
```


### Code Build - buildspec

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
  post_build:
    commands:
      - echo Entered the post_build phase...
~~~~


### Code Build - Source

<img src="images/codebuild_source.png" height="450px"/>


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


## Further information

* [CodePipeline](https://aws.amazon.com/codepipeline/)
* [CodeCommit](https://aws.amazon.com/de/codecommit/)
* [CodeBuild](https://aws.amazon.com/de/codebuild/)
* [CodeDeploy](https://aws.amazon.com/de/codedeploy/)
* [CodeBuild Provided Images](https://docs.aws.amazon.com/de_de/codebuild/latest/userguide/build-env-ref-available.html)
* [Pulumi](https://pulumi.io/)
