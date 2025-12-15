// STEP 1

// pipeline {
//     agent any
//     stages {
//         stage('Build') {
//             steps {
//                 echo "Building the project..."
//                 sh 'ls -la'
//             }
//         }
//         stage('Test') {
//             steps {
//                 echo "Running tests..."
//             }
//         }
//         stage('Deploy') {
//             steps {
//                 echo "Deploying the project..."
//             }
//         }
//     }
// }

// STEP 2

pipeline {
    agent any

    environment {
        APP_NAME = "node-web-app"
        DOCKER_IMAGE = "bthiery/node-web-app"
        DOCKER_TAG = "latest"
        CONTAINER_NAME = "node-web-app-container"
        DOCKER_CREDENTIALS_ID = "docker-hub-credential-id"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                script {
                    sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true

                    docker run -d \
                    --name ${CONTAINER_NAME} \
                    -p 3000:3000 \
                    ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully 🚀"
        }
        failure {
            echo "Deployment failed ❌"
        }
    }
}
