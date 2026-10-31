
pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    }

    stages {
        stage('Test') {
            steps {
                sh 'docker build --target test -t todo-app-test:${BUILD_NUMBER} .'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build --target final -t todo-app:${BUILD_NUMBER} .'
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh 'docker images todo-app'
            }
        }
    }
}
