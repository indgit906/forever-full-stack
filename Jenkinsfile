pipeline {
    agent any

    environment {
        DOCKERHUB_CREDS = 'dockerhub-creds'
        BACKEND_IMAGE = 'inddocker786/forever-backend'
        FRONTEND_IMAGE = 'inddocker786/forever-frontend'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('backend') {
                    sh 'docker build -t $BACKEND_IMAGE:latest .'
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('frontend') {
                    sh 'docker build -t $FRONTEND_IMAGE:latest .'
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: DOCKERHUB_CREDS,
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      docker login -u $DOCKER_USER -p $DOCKER_PASS
                      docker push $BACKEND_IMAGE:latest
                      docker push $FRONTEND_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy Containers') {
            steps {
                sh '''
                  docker stop backend frontend || true
                  docker rm backend frontend || true

                  docker run -d --name backend -p 5000:5000 $BACKEND_IMAGE:latest
                  docker run -d --name frontend -p 80:80 $FRONTEND_IMAGE:latest
                '''
            }
        }
    }
}
