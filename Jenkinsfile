pipeline {

    agent {
        label 'docker'
    }

    environment {
        IMAGE_NAME = "navadeep04/flask-k8s-demo"
        IMAGE_TAG = "2"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
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

                        echo "===== Current Deployment Image ====="

                        kubectl get deployment flask-k8s-demo \
                        -n jenkins \
                        -o jsonpath="{.spec.template.spec.containers[0].image}"

                        echo

                        echo "===== Updating Deployment ====="

                        kubectl set image deployment/flask-k8s-demo \
                        flask-k8s-demo=${IMAGE_NAME}:${IMAGE_TAG} \
                        -n jenkins

                        echo "===== Waiting for Rollout ====="

                        kubectl rollout status deployment/flask-k8s-demo \
                        -n jenkins
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'k8s-jenkins-config',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    sh '''
                        echo "===== Deployment Status ====="

                        kubectl get deployment flask-k8s-demo \
                        -n jenkins

                        echo "===== Pods ====="

                        kubectl get pods \
                        -n jenkins

                        echo "===== Deployment Image ====="

                        kubectl get deployment flask-k8s-demo \
                        -n jenkins \
                        -o jsonpath="{.spec.template.spec.containers[0].image}"

                        echo
                    '''
                }
            }
        }

        stage('Rollback') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'k8s-jenkins-config',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    sh '''
                        echo "===== Deployment History ====="

                        kubectl rollout history deployment/flask-k8s-demo \
                        -n jenkins

                        echo "===== Rolling Back Deployment ====="

                        kubectl rollout undo deployment/flask-k8s-demo \
                        -n jenkins

                        echo "===== Waiting for Rollback ====="

                        kubectl rollout status deployment/flask-k8s-demo \
                        -n jenkins

                        echo "===== Rollback Completed ====="

                        kubectl get deployment flask-k8s-demo \
                        -n jenkins

                        echo "===== Current Image ====="

                        kubectl get deployment flask-k8s-demo \
                        -n jenkins \
                        -o jsonpath="{.spec.template.spec.containers[0].image}"

                        echo
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