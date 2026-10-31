
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
stage('Deploy to Kubernetes') {
    steps {
        withCredentials([
            file(
                credentialsId: 'kubernetes-config',
                variable: 'KUBECONFIG'
            )
        ]) {
            sh '''
                set -e

                # Tag the newly built image
                docker tag todo-app:${BUILD_NUMBER} \
                    suvethican/todo-app:${BUILD_NUMBER}

                # Load image into the local kind cluster
                kind load docker-image \
                    suvethican/todo-app:${BUILD_NUMBER} \
                    --name devops-lab

                # Update the Kubernetes Deployment
                kubectl set image deployment/todo-app \
                    todo-app=suvethican/todo-app:${BUILD_NUMBER}

                # Wait for successful deployment
                kubectl rollout status deployment/todo-app \
                    --timeout=180s

                # Verify the running pods
                kubectl get pods -l app=todo-app
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
