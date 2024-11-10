pipeline {
    agent {
        label 'nodejs-slave'
    }
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE = 'thailx/react-app'
        CI = 'false'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Get Git Commit Hash') {
            steps {
                script {
                    def gitCommit = sh(script: 'git rev-parse --short=6 HEAD', returnStdout: true).trim()
                    DOCKER_TAG = gitCommit
                    echo "Git Commit Hash: ${gitCommit}"
                    echo "Git Commit Hash: ${DOCKER_TAG}"
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com/', DOCKER_CREDENTIALS_ID) {
                        echo 'Logged into Docker Hub'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }
    }
}
