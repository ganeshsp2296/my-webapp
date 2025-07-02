pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-credentials') // ID of Jenkins credential
        DOCKERHUB_REPO = "ganesh2296/my-webapp"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/ganeshsp2296/my-webapp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    COMMIT_HASH = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
                    IMAGE_TAG = "${DOCKERHUB_REPO}:${COMMIT_HASH}"
                    LATEST_TAG = "${DOCKERHUB_REPO}:latest"
                    sh "docker build -t ${IMAGE_TAG} ."
                    sh "docker tag ${IMAGE_TAG} ${LATEST_TAG}"
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${IMAGE_TAG}"
                sh "docker push ${LATEST_TAG}"
            }
        }
    }

    post {
        success {
            echo "Docker image build and push completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check the logs for errors."
        }
    }
}

