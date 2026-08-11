pipeline {

    agent {
        label 'docker'
    }

    environment {
        IMAGE_NAME = 'navadeep04/flask-k8s-demo'
        IMAGE_TAG  = '1'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
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
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'k8s-jenkins-config',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    sh '''
                        echo "===== Kubernetes Context ====="
                        kubectl config current-context

                        echo "===== Deploying Application ====="
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml

                        echo "===== Deployment ====="
                        kubectl get deployment flask-k8s-demo -n jenkins

                        echo "===== Pods ====="
                        kubectl get pods -n jenkins
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}