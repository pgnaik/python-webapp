pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "pgn123/python-webapp"
        DOCKER_TAG   = "latest"
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
                echo "Running basic syntax check..."
                // Simple syntax check of app.py
                bat 'C:\\Users\\USER\\anaconda3\\python.exe -m py_compile app.py'
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
                        // Windows-friendly docker login using Jenkins credentials
                        bat "docker login -u %USER% -p %PASS%"
                        bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Stop and remove existing container if running (Windows cmd syntax)
                    bat "docker rm -f python-webapp || exit /b 0"

                    // Run new container
                    bat "docker run -d --name python-webapp -p 8080:5000 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo "Deployment success! Open: http://<jenkins-server-ip>:8080"
        }
        failure {
            echo "Build or deployment failed."
        }
    }
}
