pipeline {
    agent any // Runs on any available Jenkins agent

    environment {
        // Use the credentials ID you created in Jenkins
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials')
        // Define your Docker Hub username and image name
        DOCKERHUB_USERNAME = "santanugarai"
        IMAGE_NAME = "pipelinetest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Jenkins automatically checks out the code from the configured repository
                echo "Checking out source code..."
                git branch: 'main', url: 'https://github.com/SantanuGarai/CICDPipelineTest.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image and tag it with the build number
                    echo "Building Docker image..."
                    def dockerImage = docker.build("${DOCKERHUB_USERNAME}/${IMAGE_NAME}:late")
               }
             
            }
        }

        stage('Push to Docker Hub') {
            steps {
                 withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    script {
                        echo "pushing Docker image to docker hub..."
                        docker.image("${IMAGE_NAME}:latest").push()
                    }
                }
            }
        }

        stage('Deploy New Container') {
            steps {
                echo "Deploying the new container..."
                sh """
                    # Stop and remove the old container if it exists
                    docker stop ${IMAGE_NAME} || true
                    docker rm ${IMAGE_NAME} || true

                    # Pull the latest image from Docker Hub
                    docker pull ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:late

                    # Run the new container
                    docker run -d --name ${IMAGE_NAME} -p 3000:3000 ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:late
                """
            }
        }
    }
}