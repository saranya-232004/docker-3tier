pipeline {
    agent any

    environment {
        BACKEND_IMAGE = 'backend-image:latest'
        FRONTEND_IMAGE = 'frontend-image:latest'
        MONGO_URI = 'mongodb://mongo:27017/mydb'
        MONGO_CONTAINER = 'mongo:latest'
        BACKEND_PORT = '3001'
        FRONTEND_PORT = '3000'
        MONGO_PORT = '27017'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout code based on branch
                    checkout scm
                }
            }
        }

        stage('Backend Build & Test') {
            steps {
                script {
                    // Backend build (assumes Node.js backend for example)
                    echo "Building Backend"
                    sh 'cd backend && npm install && npm test'

                    // You can modify this for Java, Python, etc.
                    // sh 'cd backend && mvn clean install'
                    // sh 'cd backend && pytest'
                }
            }
        }

        stage('Frontend Build & Test') {
            steps {
                script {
                    // Frontend build (assuming a React app as an example)
                    echo "Building Frontend"
                    sh 'cd frontend && npm install && npm run build'

                    // If using another framework like Angular, Vue.js, or others, change accordingly.
                    // sh 'cd frontend && ng build --prod'
                }
            }
        }

        stage('Deploy MongoDB') {
            steps {
                script {
                    // Deploy MongoDB container (can be customized as needed)
                    echo "Deploying MongoDB"
                    sh """
                        docker pull ${MONGO_CONTAINER}
                        docker run --name mongo -d -p ${MONGO_PORT}:${MONGO_PORT} ${MONGO_CONTAINER}
                    """
                    // You can customize this step based on your preferred MongoDB setup.
                    // This step assumes MongoDB is deployed via Docker.
                }
            }
        }

        stage('Deploy Backend') {
            steps {
                script {
                    echo "Deploying Backend"
                    sh """
                        docker build -t ${BACKEND_IMAGE} ./backend
                        docker run -d -p ${BACKEND_PORT}:${BACKEND_PORT} --link mongo:${MONGO_URI} ${BACKEND_IMAGE}
                    """
                    // Assuming Dockerized backend, replace with appropriate deploy step.
                    // You could also deploy to Kubernetes or another service.
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                script {
                    echo "Deploying Frontend"
                    sh """
                        docker build -t ${FRONTEND_IMAGE} ./frontend
                        docker run -d -p ${FRONTEND_PORT}:${FRONTEND_PORT} ${FRONTEND_IMAGE}
                    """
                    // For frontend, if you're using Nginx, Dockerize it.
                    // You may need to configure it for production (e.g., CDN, etc.).
                }
            }
        }

        stage('Post Deployment Tests') {
            steps {
                script {
                    // Run post-deployment tests (optional)
                    echo "Running Post Deployment Tests"
                    // Example: curl the backend and frontend to ensure they are working
                    sh 'curl -f http://localhost:${BACKEND_PORT}/api/healthcheck'
                    sh 'curl -f http://localhost:${FRONTEND_PORT}'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline successful, application deployed successfully.'
        }
        failure {
            echo 'Pipeline failed, there were errors during deployment.'
        }
        always {
            cleanWs()
        }
    }
}

