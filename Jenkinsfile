
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

stage('Push to Docker Hub') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub-credentials',
                usernameVariable: 'DOCKERHUB_USERNAME',
                passwordVariable: 'DOCKERHUB_TOKEN'
            )
        ]) {
            sh '''
                set +x

                echo "$DOCKERHUB_TOKEN" | docker login \
                    -u "$DOCKERHUB_USERNAME" --password-stdin

                docker tag todo-app:${BUILD_NUMBER} \
                    "$DOCKERHUB_USERNAME/todo-app:${BUILD_NUMBER}"

                docker push "$DOCKERHUB_USERNAME/todo-app:${BUILD_NUMBER}"

                docker logout
            '''
        }
    }
}

        stage('Verify Docker Image') {
            steps {
                sh 'docker images todo-app'
            }
        }
    }
}
