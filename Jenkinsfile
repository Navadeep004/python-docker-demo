@Library('devops-lib') _

pipeline {

    agent {
        label 'docker'
    }

    stages {

        stage('Library Test') {

            steps {

                hello()

            }

        }

        stage('Docker Build') {

            steps {

                dockerBuild("python-demo")

            }

        }

    }

}