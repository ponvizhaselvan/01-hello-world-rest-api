pipeline {
    agent any

    environment {
        // Change this to your DockerHub username
        DOCKER_HUB_USER = 'ponvizha'
        IMAGE_NAME = 'restful-web-services'
    }

//Automatically checkout the code from github repository which is mentioned while creating pipeline job
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Prepare Dependencies for Docker') {
            steps {
                bat '''
                    if not exist target\\dependency mkdir target\\dependency
                    cd target
                    for %%f in (*.jar) do (
                        jar -xf "%%f"
                    )
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t %DOCKER_HUB_USER%/%IMAGE_NAME%:latest ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat '''
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                        docker push %DOCKER_HUB_USER%/%IMAGE_NAME%:latest
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build and Docker push successful!'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
