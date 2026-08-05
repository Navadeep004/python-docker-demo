pipeline {

    agent {
        label 'docker'
    }

    environment {

        IMAGE_NAME = "python-demo"

        IMAGE_TAG = "${BUILD_NUMBER}"

        DOCKER_USERNAME = "YOUR_DOCKER_USERNAME"

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
                docker build \
                -t ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG} .
                '''

            }

        }

        stage('Verify Image') {

            steps {

                sh 'docker images'

            }

        }

        stage('Docker Login') {

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-login',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo "$DOCKER_PASS" | docker login \
                    -u "$DOCKER_USER" \
                    --password-stdin
                    '''

                }

            }

        }

        stage('Push Image') {

            steps {

                sh '''
                docker push ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}
                '''

            }

        }

    }

    post {

        success {

            echo "Pipeline Completed Successfully!"

        }

        failure {

            echo "Pipeline Failed"

        }

        always {

            sh 'docker logout || true'

        }

    }

}