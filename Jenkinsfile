pipeline {
    agent any

    environment {
        IMAGE_NAME = 'afral507/jenkins-image'
        CONTAINER_NAME = 'jenkins-docker-app'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/Afra1507/jenkinsTest.git', branch: 'main'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    bat '''
                        echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                    '''
                }
            }
        }

        stage('Build Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:latest .'
            }
        }

        stage('Run Container') {
            steps {
                bat '''
                    docker rm -f %CONTAINER_NAME%
                    docker run -d -p 3000:3000 --name %CONTAINER_NAME% %IMAGE_NAME%:latest
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                bat 'docker push %IMAGE_NAME%:latest'
            }
        }
    }

    post {
        success {
            echo "✅ App built, container running, image pushed to Docker Hub!"
        }
        failure {
            echo "❌ Pipeline failed."
        }
    }
}
