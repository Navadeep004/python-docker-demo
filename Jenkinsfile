pipeline {

    agent {
        label 'docker'
    }

    environment {
        IMAGE_NAME = 'navadeep04/jenkins-k8s-demo'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
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
                sh """
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'kubeconfig-jenkins',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    sh """
                        kubectl set image deployment/jenkins-k8s-demo \
                        jenkins-k8s-demo=${IMAGE_NAME}:${IMAGE_TAG} \
                        -n jenkins

                        kubectl rollout status deployment/jenkins-k8s-demo \
                        -n jenkins
                    """
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}