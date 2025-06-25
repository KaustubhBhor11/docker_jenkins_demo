pipeline {
    agent any

    environment {
        GIT_REPOSITORY_URL = 'https://github.com/KaustubhBhor11/docker_jenkins_demo.git'
        DOCKER_IMAGE_NAME = 'kaustubh383/hello-world-python'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    try {
                        git branch: 'main', url: GIT_REPOSITORY_URL
                    } catch (Exception e) {
                        echo "Failed to clone repository: ${e.message}"
                        error "Failed to clone repository"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        docker.build("${DOCKER_IMAGE_NAME}:${IMAGE_TAG}")
                    } catch (Exception e) {
                        echo "Failed to build Docker image: ${e.message}"
                        error "Failed to build Docker image"
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'my-dockerhub-hello-world-id',
                                                  usernameVariable: 'DOCKER_USERNAME',
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        try {
                            bat """
                                echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin
                                docker tag ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_IMAGE_NAME}:${IMAGE_TAG}
                                docker push ${DOCKER_USERNAME}/${DOCKER_IMAGE_NAME}:${IMAGE_TAG}
                            """
                        } catch (Exception e) {
                            echo "Failed to push Docker image to registry: ${e.message}"
                            error "Failed to push Docker image"
                        }
                    }
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    try {
                        bat "docker image rm ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} || echo 'Image not found or already removed'"
                    } catch (Exception e) {
                        echo "Cleanup step failed: ${e.message}"
                    }
                }
            }
        }
    }
}
