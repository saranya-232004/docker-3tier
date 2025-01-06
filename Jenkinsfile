pipeline {
    agent any

    environment {
        FRONTEND_DIR = 'frontend'
        BACKEND_DIR = 'backend'
        MONGO_CONTAINER_NAME = 'mongo-db'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the code from the branch
                    checkout scm
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir("${env.FRONTEND_DIR}") {
                    script {
                        // Build frontend code
                        sh 'npm install'
                        sh 'npm run build'
                    }
                }
            }
        }

        stage('Build Backend') {
            steps {
                dir("${env.BACKEND_DIR}") {
                    script {
                        // Build backend code
                        sh 'npm install'
                        sh 'npm run build'
                    }
                }
            }
        }

        stage('Deploy MongoDB') {
            steps {
                script {
                     // Deploy MongoDB using Docker
                    sh 'docker pull mongo:latest'
                    sh 'docker run -d --name ${env.MONGO_CONTAINER_NAME} -p 27017:27017 mongo'
                }
            }
        }

        stage('Deploy Backend') {
            steps {
                dir("${env.BACKEND_DIR}") {
                    script {
                        // Build and run backend in Docker
                        sh 'docker build -t sarandocker23/backend-image .'
                        sh 'docker run -d --name backend-app --link ${env.MONGO_CONTAINER_NAME}:mongo -p 3001:3001 sarandocker23/backend-image'
                    }
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                dir("${env.FRONTEND_DIR}") {
                    script {
                        // Build and run frontend in Docker
                        sh 'docker build -t sarandocker23/frontend-image .'
                        sh 'docker run -d --name frontend-app -p 3000:3000 sarandocker23/frontend-image'
                    }
                }
            }


        stage('Test') {
            steps {
                script {
                    // Run tests (adjust as needed)
                    sh 'npm test'  // Assuming unit tests or integration tests
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    // Optionally clean up containers after deployment
                    sh 'docker stop frontend-app backend-app ${env.MONGO_CONTAINER_NAME}'
                    sh 'docker rm frontend-app backend-app ${env.MONGO_CONTAINER_NAME}'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
        success {
            echo 'Deployment successful'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}


