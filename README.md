# one2onetool
This CI/CD pipeline is triggered on new commits and performs the following:
1. Build and Run tests on the application
2. Containerise and deploy the application on a public cloud instance
3. "Staging" branch uses "Questions-test.json" as input data file, while "Release" branch uses "Questions.json" as input data file
4. Versioning to differentiate builds
5. If any of the tasks fail at any point, the pipeline stops and an email alert is sent with relevant information

# Instructions
2 branches - Staging and Release - were added with their own Jenkinsfile. Create a Multibranch Pipeline job in Jenkins and link it to this repository.

## Set up Jenkins
Install Jenkins for Windows: https://www.jenkins.io/doc/book/installing/windows/

### Install plugins
* Multibranch Scan Webhook Trigger (for using Webhook with GitHub)

### Set up appropriate JDK version in Manage Jenkins > Global Tool Configuration
This will be used for Jenkins to build the job, so use a version that is compatible with the project.

### Create WebHook in GitHub (to trigger Jenkins build upon commit to GitHub repository)
Guide: https://docs.github.com/en/webhooks-and-events/webhooks/creating-webhooks

### Create a new Multibranch Pipeline job
A multibranch pipeline job scans the repository and looks for the `Jenkinsfile` in each branch to run a build on
* Select `GitHub hook trigger for GITScm polling` as Build Trigger for Build Configuration