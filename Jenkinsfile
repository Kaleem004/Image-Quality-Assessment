pipeline {
    agent any

    environment {
        DOCKER_ID = "rohitsontamu"
        DOCKER_IMAGE_API = "iqa-cicd-api"
        DOCKER_IMAGE_STREAMLIT = "iqa-cicd-streamlit"
        IMAGE_TAG = "${BUILD_ID}"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Rohitsontamu/Image-quality-assessment-cnn.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_ID}/${DOCKER_IMAGE_API}:${IMAGE_TAG} -f Dockerfile.api ."
                    sh "docker build -t ${DOCKER_ID}/${DOCKER_IMAGE_STREAMLIT}:${IMAGE_TAG} -f Dockerfile.streamlit ."
                }
            }
        }

        stage('Run with Docker Compose') {
            steps {
                script {
                    sh 'docker-compose down || true'
                    sh 'docker-compose up -d --build'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub', 
                        usernameVariable: 'DOCKERHUB_USERNAME', 
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    )]) {
                        sh '''
                            echo "$DOCKERHUB_PASSWORD" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                            docker push ${DOCKER_ID}/${DOCKER_IMAGE_API}:${IMAGE_TAG}
                            docker push ${DOCKER_ID}/${DOCKER_IMAGE_STREAMLIT}:${IMAGE_TAG}
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD pipeline completed successfully!'
        }
        failure {
            echo '❌ CI/CD pipeline failed.'
        }
    }
}
