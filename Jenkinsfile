pipeline {

    agent any

    stages {

        stage('Build') {
            steps {
                echo "Building Application..."
                sleep 5
            }
        }

        stage('Parallel Tasks') {

            parallel {

                stage('Unit Test') {
                    steps {
                        echo "Running Unit Tests..."
                        sleep 10
                    }
                }

                stage('Lint') {
                    steps {
                        echo "Running Lint Check..."
                        sleep 10
                    }
                }

                stage('Security Scan') {
                    steps {
                        echo "Running Security Scan..."
                        sleep 10
                    }
                }

            }

        }

        stage('Deploy') {
            steps {
                echo "Deploying Application..."
            }
        }

    }

}