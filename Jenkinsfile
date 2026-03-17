pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = '2023BEC0014'
        DOCKER_CREDS_ID = 'ritishaa_anand'
        DOCKER_HUB_USER = 'anandritishaa07'
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker Image: ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:${TAG}..."
                    sh "docker build -t ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:${TAG} -t ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    sh "docker run -d --rm --name temp-test-${TAG} -p 8090:80 ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:${TAG}"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDS_ID, usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                        sh """
                        echo \$DOCKERHUB_PASS | docker login -u ${DOCKER_HUB_USER} --password-stdin
                        docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:${TAG}
                        docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:latest
                        docker logout
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo "Build and Push was successful!"
        }
        failure {
            echo "Build or Push failed."
        }
    }
}
