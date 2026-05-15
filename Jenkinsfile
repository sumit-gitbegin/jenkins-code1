pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "myapp:${env.BUILD_NUMBER}"
        DOCKER_REGISTRY = "sumitgitbegin" // replace with your Docker Hub username
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/sumit-gitbegin/jenkins-code1.git', branch: 'main'
            }
        }

        stage('Verify Dockerfile') {
            steps {
                sh 'pwd'
                sh 'ls -l'
                sh 'cat Dockerfile'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Explicitly specify Dockerfile
                    sh "docker build -f Dockerfile -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'DOCKER_USERNAME',  // Jenkins Docker Hub credentials ID
                    usernameVariable: 'USERNAME', 
                    passwordVariable: 'PASSWORD'
                )]) {
                    script {
                        sh "echo $PASSWORD | docker login -u $USERNAME --password-stdin"
                        sh "docker tag ${DOCKER_IMAGE} ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
                        sh "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('Deploy App') {
            steps {
                script {
                    // Stop and remove old container if exists
                    sh "docker rm -f myapp || true"
                    // Run new container
                    sh "docker run -d --name myapp -p 8080:8080 ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
                }
            }
        }

    }

    post {
        always {
            // Clean up local images
            sh "docker rmi ${DOCKER_IMAGE} || true"
        }
        success {
            echo "Deployment completed successfully!"
        }
        failure {
            echo "Deployment failed."
        }
    }
}
