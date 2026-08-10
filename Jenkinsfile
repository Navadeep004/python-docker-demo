pipeline {

    agent {
        label 'docker'
    }

    stages {

        stage('Docker Build') {

            steps {

                sh '''
                    docker build -t navadeep04/python-demo:7 .
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
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''

                }

            }

        }

        stage('Push Image') {

            steps {

                sh '''
                    docker push navadeep04/python-demo:7
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

                        echo ""
                        echo "===== Deploying Job ====="
                        kubectl apply -f job.yaml

                        echo ""
                        echo "===== Job Status ====="
                        kubectl get jobs -n jenkins

                        echo ""
                        echo "===== Pod Status ====="
                        kubectl get pods -n jenkins

                        echo ""
                        echo "===== Waiting for Job ====="
                        kubectl wait --for=condition=complete job/python-demo -n jenkins --timeout=120s

                        echo ""
                        echo "===== Application Logs ====="
                        kubectl logs job/python-demo -n jenkins
                    '''

                }

            }

        }

    }

    post {

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }

        always {
            sh 'docker logout || true'
        }

    }

}