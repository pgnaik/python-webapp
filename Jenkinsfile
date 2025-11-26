pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "pgn123/python-webapp"  
        DOCKER_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                // Pull latest code from GitHub
                git branch: 'main', url: 'https://github.com/pgnaik/python-webapp.git'
            }
        }

        stage('Build & Test') {
            steps {
                // (Optional) Create virtualenv or just run basic tests
                echo "Running basic syntax check..."
                // For simple script, we might just do:
                bat 'python -m py_compile app.py'
            }
        }
      
        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CRED',
                                                     usernameVariable: 'USER',
                                                     passwordVariable: 'PASS')]) {
                        bat "echo $PASS | docker login -u $USER --password-stdin"
                        bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }
