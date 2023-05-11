pipeline {

    agent any

    tools {
        jdk 'Java Version 17.0.7'
    }

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION = 'ap-southeast-1'
        ECS_TASK_DEFINITION = 'one2onetool-release-task-def'
        ECS_SERVICE_NAME = 'one2onetool-release-service'
        ECS_CLUSTER_NAME = 'one2onetool-release-cluster'
    }

    stages {

        stage("Test Application") {
            steps {
                sh '''
                    npm install
                    npm test
                '''
            }
        }
        stage("Build Docker Image") {
            steps {
                echo "Building Docker image"
                sh "docker build -t one2onetool ."
            }
        }
        stage("Tag Docker Image") {
            steps {
                echo "Tagging Docker image"
                sh "docker tag one2onetool:latest one2onetool:RELEASE-${BUILD_TAG}"
            }
        }
        stage("Push Docker Image to ECR") {
            steps {
                echo "Pushing Docker Image to ECR"
                script {
                    docker.withRegistry('https://<AWS-Account-Number>.dkr.ecr.ap-southeast-1.amazonaws.com','ecr:ap-southeast-1:<Credential-ID>') {
                        sh "docker push <AWS-Account-Number>.dkr.ecr.ap-southeast-1.amazonaws.com/one2onetool:RELEASE-${BUILD_TAG}"
                    }
                }
            }
        }
        stage("Deploy to ECS") {
            steps {
                echo "Deploying to ECS"
                script {
                    ecsDeploy(
                        accessKey: "${AWS_ACCESS_KEY_ID}",
                        secretKey: "${AWS_SECRET_ACCESS_KEY}",
                        region: "${AWS_DEFAULT_REGION}",
                        taskDefinition: "${ECS_TASK_DEFINITION}",
                        service: "${ECS_SERVICE_NAME}",
                        cluster: "${ECS_CLUSTER_NAME}",
                        image: "${one2onetool:RELEASE-${BUILD_TAG}"}",
                        waitForServiceStability: true
                    )
                }
            }
        }
    }

    post {

        always {
            echo "Cleaning up the workspace"
            sh "docker rmi one2onetool:RELEASE-${BUILD_TAG} one2onetool:latest"         
        }

        success {
            script {
                echo "Jenkins build success. Generating Git Tag"
                withCredentials([string(credentialsId: 'GITHUB-PAT', variable: 'GIT_TOKEN')]) {
                    sh '''
                        git tag -a RELEASE-${BUILD_TAG} -m 'Jenkins build success: RELEASE-${BUILD_TAG}'
                        git push origin RELEASE-${BUILD_TAG}
                    '''
                }
            }
        }

        failure {
            script {
                echo "Jenkins build failed. Generating Git Tag"
                withCredentials([string(credentialsId: 'GITHUB-PAT', variable: 'GIT_TOKEN')]) {
                    sh '''
                        git tag -a RELEASE-${BUILD_TAG} -m 'Jenkins build RELEASE-${BUILD_TAG} failed at stage ${STAGE_NAME}'
                        git push origin RELEASE-${BUILD_TAG}
                    '''
                }

                echo "Send email"
                mail to: "dev@gmail.com", 
                subject: "Jenkins Build Failure: RELEASE-${BUILD_TAG}", 
                body: "Jenkins build RELEASE-${BUILD_TAG} failed at stage ${STAGE_NAME}"
            }
        }
    }
}