pipeline {

    agent {
        label 'docker'
    }

    stages {

        stage('Docker Build') {
            steps {
                sh 'docker build -t navadeep04/jenkins-k8s-demo:1 .'
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
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push navadeep04/jenkins-k8s-demo:1'
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

                        echo "===== Deployments ====="
                        kubectl get deployments -n jenkins

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