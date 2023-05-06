pipeline {

    agent any

    tools {
        jdk 'Java Version 17.0.7'
    }

    stages {

        stage("Testing Before Containerisation") {
            steps {
                sh '''
                    npm install
                    npm test
                '''
            }
        }
        stage("Build") {
            steps {
                echo "Building Docker image"
                sh '''
                    docker build -t one2onetool .
                    docker tag one2onetool:latest one2onetool:${BUILD_TAG}
                '''
            }
        }
        stage("Start Application Container") {
            steps {
                echo "Starting application container"
                sh '''
                    docker run -p 3000:3000 --name one2onetoolTest one2onetool:${BUILD_TAG} -d
                '''
            }
        }
        stage("Test Containerised Application") {
            steps {
                echo "Testing container application"
                sh '''
                    docker exec -it one2onetoolTest npm test
                '''
            }
        }
        stage("Deploy to Cloud") {
            when {
                branch 'Release'
            }
            steps {
                withAWS(region: 'ap-southeast-1') {
                    //deploy release to cloud
                }                    
            }
        }

        post {
            always {
                echo "Cleaning up the workspace"
                dir("${WORKSPACE) {
                    sh '''
                        docker rm -f one2onetoolTest
                        docker rmi one2onetool:${BUILD_TAG}
                    '''
                }                
            }
            success {
                script {
                    echo "Jenkins build success. Generating Git Tag"
                    sh '''
                        git tag -a ${BUILD_TAG} -m 'Jenkins build success: ${BUILD_TAG}'
                        git push origin ${BUILD_TAG}
                    '''
                }
            }
            failure {
                script {
                    echo "Jenkins build failed. Generating Git Tag"
                    sh '''
                        git tag -a ${BUILD_TAG} -m 'Jenkins build ${BUILD_TAG} failed at stage ${STAGE_NAME}'
                        git push origin ${BUILD_TAG}
                    '''
                }
            }
        }

    }
}