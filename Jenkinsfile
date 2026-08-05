pipeline {

    agent {
        label 'docker'
    }

    stages {

        stage('Agent Information') {

            steps {

                sh 'echo "Running on Jenkins Agent"'

                sh 'hostname'

                sh 'pwd'

                sh 'whoami'

            }

        }

    }

}