pipeline {

    agent {
        label 'docker'
    }

    environment {
        IMAGE_NAME = "python-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_USER = "navadeep04"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Application Build Started..."
            }
        }

        stage('Quality Checks') {

            failFast true

            parallel {

                stage('Unit Test') {
                    steps {
                        echo "Running Unit Tests..."
                        sleep 5
                    }
                }

                stage('Lint Check') {
                    steps {
                        echo "Running Lint Check..."
                        sleep 5
                    }
                }

                stage('Security Scan') {
                    steps {
                        echo "Running Security Scan..."
                        sleep 5
                    }
                }

            }
        }

        stage('Docker Build') {
            steps {
                sh """
                docker build \
                -t ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh "docker images"
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-login',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh '''
                    echo "$PASS" | docker login \
                    -u "$USER" \
                    --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                docker push ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }

    post {

        success {
            echo "Pipeline Completed Successfully!"
        }

        failure {
            echo "Pipeline Failed!"
        }

        always {
            echo "Cleaning Up..."
            sh "docker logout || true"
        }
    }
}