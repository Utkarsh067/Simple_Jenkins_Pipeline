pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-jenkins-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Test Docker Image') {
            steps {
                // Run container in detached mode
                sh "docker run -d --name test-container -p 5000:80 ${IMAGE_NAME}:${IMAGE_TAG}"
                // Wait for a few seconds for container to start
                sh "sleep 5"
                // Simple curl test to check app is running
                sh "curl -f http://localhost:5000 || exit 1"
                // Stop and remove container after test
                sh "docker stop test-container && docker rm test-container"
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploy stage: (local) You can add deployment commands here"
                // For example, run the container for your app on localhost (detached)
                sh """
                docker rm -f flask-jenkins-app-running || true
                docker run -d --name flask-jenkins-app-running -p 80:80 ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        always {
            sh "docker system prune -f"
        }
        success {
            echo "Pipeline completed successfully."
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
