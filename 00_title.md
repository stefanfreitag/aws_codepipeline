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



Show example use cases
 Creation of pipeline for
  Compiling code and storing the artifact
  use code deploy to deploy to ec2 instances
  use code build to create docker image
  use code deploy to ECS (Fargate)





### Code Commit

* git-based source code repository
* HTTPS- and SSH-based access
* You must use a Git client that supports Git version 1.7.9 or later to connect to an AWS CodeCommit repository
* You must have an AWS CodeCommit managed policy attached to your IAM user,


### Code Build

* specify the location of your source code, choose your build settings, and CodeBuild will run build scripts for compiling, testing, and packaging your code. There are no servers to provision and scale, or software to install, configure, and operate. (Source: https://aws.amazon.com/codebuild/features/?nc=sn&loc=2)

*  AWS CodeBuild stellt Build-Umgebungen für Java, Python, Node.js, Ruby, Go, Android, NET Core für Linux und Docker bereit.
* Eine Build-Umgebung stellt eine Kombination aus Betriebssystem, Programmiersprachenlaufzeit und Tools dar, die CodeBuild zum Ausführen eines Builds verwendet. 

```shell
aws codebuild list-curated-environment-images
```


| Plattform	| Programmiersprache oder Framework	| Laufzeitversion	| Image-Kennung	| Definition
| :-------------:| :-------------:| :-------------:| :-------------:|| :-------------:|
Ubuntu 14.04	(Basis-Image)		aws/codebuild/ubuntu-base:14.04	ubuntu/ubuntu-base/14.04
Ubuntu 14.04	Android	26.1.1	aws/codebuild/android-java-8:26.1.1	ubuntu/android-java-8/26.1.1
Ubuntu 14.04	Docker	18.09.0	aws/codebuild/docker:18.09.0	ubuntu/docker/18.09.0
Ubuntu 14.04	Docker	17.09.0	aws/codebuild/docker:17.09.0	ubuntu/docker/17.09.0
Ubuntu 14.04	Golang	1.11	aws/codebuild/golang:1.11	ubuntu/golang/1.11
Ubuntu 14.04	Golang	1.10	aws/codebuild/golang:1.10	ubuntu/golang/1.10
Ubuntu 14.04	Java	11	aws/codebuild/java:openjdk-11	ubuntu/java/openjdk-11
Ubuntu 14.04	Java	9	aws/codebuild/java:openjdk-9	ubuntu/java/openjdk-9
Ubuntu 14.04	Java	8	aws/codebuild/java:openjdk-8	ubuntu/java/openjdk-8
Ubuntu 14.04	Node.js	10.14.1	aws/codebuild/nodejs:10.14.1	ubuntu/nodejs/10.14.1
Ubuntu 14.04	Node.js	8.11.0	aws/codebuild/nodejs:8.11.0	ubuntu/nodejs/8.11.0
Ubuntu 14.04	PHP	7.1	aws/codebuild/php:7.1	ubuntu/php/7.1
Ubuntu 14.04	Python	3.7.1	aws/codebuild/python:3.7.1	ubuntu/python/3.7.1
Ubuntu 14.04	Python	3.6.5	aws/codebuild/python:3.6.5	ubuntu/python/3.6.5
Ubuntu 14.04	Ruby	2.5.3	aws/codebuild/ruby:2.5.3	ubuntu/ruby/2.5.3
Ubuntu 14.04	.NET Core	2.1	aws/codebuild/dot-net:core-2.1	ubuntu/dot-net/core-2.1
Windows Server Core 2016	(Basis-Image)		aws/codebuild/windows-base:1.0	
CodeBuild verwaltet auch die folgenden Docker-Images, die nicht in den CodeBuild- und CodePipeline-Konsolen verfügbar sind.


## CodeDeploy
deployment service that automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services.

# Pros and Cons

* Automated setup via CDK
* Integrates pretty well with other AWS services

## What we tried so far with TeamCity

* Increase control and automation
  * Move to own build servers
  * Automate the installation and configuration on the agents
  * Build configuration as code


# TODO

* Login to the [AWS console](https://aws.amazon.com/de/console/)



### Prepare the Elasitc Beanstalk application

* Click on _Create New Application_
* Application name is _NodeJsDemo_
* Click _Action_, _Create environment_
* Select _Web server environment_


* Update environment name
* Find a free domain name
* Select Node.js as platform
* Select _Configure more options_
* Select _High Availability_ from configuration presets
* Click _Create environment_

Open browser at provided URL



### Creating a repository

* Go to _CodeCommit_ service
* Click _Create Repository_
* Provide repository name and (optional) description

~~~~sh
Name: nodejsDemo
Description: Repository storing the code for the NodeJs Demo
~~~~

* Click _Create_


* Click on _Clone URL_
* Click _Clone HTTPS_ to get the endpoint

~~~~sh
https://git-codecommit.eu-west-1.amazonaws.com/v1/repos/nodejsDemo
~~~~


* Switch to _IAM_ service and create a user
* Setup _HTTPS Git credentials for AWS CodeCommit_
* Save username and password.
  
  Both are required when interacting with repository
* Push code to repository



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

The  missing YAML file is a [build specification](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)


* Add a buildspec.yml to the git repository and commit

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



## Further information

* [CodePipeline](https://aws.amazon.com/codepipeline/)
* [CodeCommit](https://aws.amazon.com/de/codecommit/)
* [CodeBuild](https://aws.amazon.com/de/codebuild/)
* [CodeDeploy](https://aws.amazon.com/de/codedeploy/)
* [ElasticBeanstalk](https://aws.amazon.com/de/elasticbeanstalk/)
* [NodeJS Demo](https://docs.aws.amazon.com/de_de/elasticbeanstalk/latest/dg/RelatedResources.html)


* [CodeBuild Provided Images](https://docs.aws.amazon.com/de_de/codebuild/latest/userguide/build-env-ref-available.html)