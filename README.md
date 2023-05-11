# one2onetool
This CI/CD pipeline is triggered on new commits and performs the following:
1. Build and Run tests on the application
2. Containerise and deploy the application on a public cloud instance
3. "Staging" branch uses "Questions-test.json" as input data file, while "Release" branch uses "Questions.json" as input data file
4. Versioning to differentiate builds
5. If any of the tasks fail at any point, the pipeline stops and an email alert is sent with relevant information

# Instructions
2 branches - Staging and Release - were added with their own Jenkinsfile. Create a Multibranch Pipeline job in Jenkins and link it to this repository.

## Add the following parameters to Jenkinsfile:
* AWS-Account-Number
* Credential-ID

## Set up Jenkins
Install Jenkins for Windows: https://www.jenkins.io/doc/book/installing/windows/
* Choose to install recommended plugins

### Install additional plugins
* Multibranch Scan Webhook Trigger (for using Webhook with GitHub)
* Amazon ECR
* Amazon Elastic Container Service (ECS) / Fargate

### Set up appropriate JDK version in Manage Jenkins > Global Tool Configuration
This will be used for Jenkins to build the job, so use a version that is compatible with the project.

### Store the following credentials in Jenkins (these are used in the Jenkinsfile)
* AWS_ACCESS_KEY_ID
* AWS_SECRET_ACCESS_KEY
* GITHUB-PAT

### Create WebHook in GitHub (to trigger Jenkins build upon commit to GitHub repository)
Guide: https://docs.github.com/en/webhooks-and-events/webhooks/creating-webhooks

### Create a new Multibranch Pipeline job
A multibranch pipeline job scans the repository and looks for the `Jenkinsfile` in each branch to run a build on
* Under Branch Sources, choose GitHub and add in GitHub repo URL
* Build Configuration
  - Mode: by Jenkinsfile
  - Script Path: Jenkinsfile (this is path for Jenkins to find Jenkinsfile in the repository)
* Select `GitHub hook trigger for GITScm polling` as Build Trigger for Build Configuration

## Set up AWS
We will deploy containerised application to AWS, so the following has to be set up before running the pipeline.

### Create ECR Repository
In AWS, create ECR Repository: one2onetool

### Create ECS Cluster, Service and Task Definition
In AWS, create
* ECS Cluster: one2onetool-release-cluster
* ECS Service: one2onetool-release-service
* Task Definition: one2onetool-release-task-def