pipeline {
    agent any

    environment {
        // Change this to your DockerHub username
        DOCKER_HUB_USER = 'ponvizha'
        IMAGE_NAME = 'restful-web-services'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Prepare Dependencies for Docker') {
            steps {
                sh 'mkdir -p target/dependency'
                sh 'cd target && jar -xf *.jar'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
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
