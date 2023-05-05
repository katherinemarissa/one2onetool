pipeline {

    tools {
        //specify java version to use (jenkins only works with 11 or 17)
        jdk 'Java Version 17.0.7'
    }

    environment {
        DATA_FILE = 'Questions-test.json'
    }

    stages {

        stage("Containerise") {
            steps {
            }
        }
        stage("Build") {
            steps {
            }
        }
        stage("Test") {
            steps {
            }
        }
        stage("Deploy to Cloud") {
            steps {
            }
        }

        post {
            always {
                //stop and remove testing container and their volumes
                dir("${workspace}/path/to/docker-compose/file") {
                    sh "docker-compose down --volumes"
                }
                //versioning
                echo "Generating Git Tag"
                sh '''
                    git tag -a ${tag} -m Jenkins build ${build_status}
                    git push origin ${tag}
                '''
            }
        }

    }
}