pipeline {

    agent any

    stages {

        stage('Parallel Demo') {

            failFast true

            parallel {

                stage('Unit Test') {

                    steps {
                        echo "Running Unit Tests..."
                        sleep 10
                    }

                }

                stage('Lint') {

                    steps {
                        error "Lint Check Failed!"
                    }

                }

                stage('Security Scan') {

                    steps {
                        echo "Running Security Scan..."
                        sleep 20
                    }

                }

            }

        }

    }

}