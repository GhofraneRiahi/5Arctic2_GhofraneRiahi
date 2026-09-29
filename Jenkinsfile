pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'ghofraneriahi'
        BACKEND_IMAGE  = "${DOCKERHUB_USER}/projets-backend"
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/projets-frontend"
        TAG            = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build images') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE:$TAG -t $BACKEND_IMAGE:latest ./backend'
                sh 'docker build -t $FRONTEND_IMAGE:$TAG -t $FRONTEND_IMAGE:latest ./frontend'
            }
        }

        stage('Login Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh 'echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin'
                }
            }
        }

        stage('Push images') {
            steps {
                sh 'docker push $BACKEND_IMAGE:$TAG'
                sh 'docker push $BACKEND_IMAGE:latest'
                sh 'docker push $FRONTEND_IMAGE:$TAG'
                sh 'docker push $FRONTEND_IMAGE:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
