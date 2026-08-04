pipeline {

    agent any

    environment {
        IMAGE_NAME = "python-demo"
        IMAGE_TAG = "v1"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Verify Image') {
            steps {
                sh '''
                docker images
                '''
            }
        }
    }

    post {
        success {
            echo "Docker Image Built Successfully!"
        }

        failure {
            echo "Docker Build Failed!"
        }

        always {
            echo "Pipeline Finished"
        }
    }
}