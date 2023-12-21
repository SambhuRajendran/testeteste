pipeline {
    agent any
    environment {
        IMAGE_NAME = 'rsambhu/nodeapp' // Replace with your image name
        REGISTRY = 'registry.hub.docker.com' // Replace with your Docker registry
        REGISTRY_CREDENTIALS_ID = 'eac6699b-b1bf-484f-97f2-68e9c861ba80' // Replace with your credentials ID
        EC2_SERVER = 'ec2-54-90-113-34.compute-1.amazonaws.com' // Replace with your EC2 server IP or hostname
        SSH_KEY = credentials('e75189c2-0bf8-4e30-8854-f911f538f7b7') // Replace with your SSH key credential ID
    }
    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/SambhuRajendran/DockerProjects.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} ."
                }
            }
        }
        stage('Push Docker Image to Registry') {
            steps {
                script {
                    // Log in to Docker registry
                    docker.withRegistry("https://${REGISTRY}", REGISTRY_CREDENTIALS_ID) {
                        // Push the image to the Docker registry
                        docker.image("${IMAGE_NAME}:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }
        stage('Deploy to EC2') {
            steps {
                script {
                    // SSH into the EC2 server
                    sh "ssh -i /var/lib/jenkins/DockerTest.pem ubuntu@${EC2_SERVER} 'sudo docker stop nodeapp-container || true && docker rm nodeapp-container || true'"
                    sh "ssh -i /var/lib/jenkins/DockerTest.pem ubuntu@${EC2_SERVER} 'sudo docker pull ${IMAGE_NAME}:${env.BUILD_NUMBER}'"
                    sh "ssh -i /var/lib/jenkins/DockerTest.pem ubuntu@${EC2_SERVER} 'sudo docker run -d -p 2001:3000 --name nodeapp-container ${IMAGE_NAME}:${env.BUILD_NUMBER}'"

                }
            }
        }
    }
}