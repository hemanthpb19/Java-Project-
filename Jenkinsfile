pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'jdk17'
    }

    environment {
        IMAGE_NAME = "devops-app"
        DOCKER_REGISTRY = "your-dockerhub-username"   // Replace with your DockerHub username
        DOCKER_TAG = "${env.BUILD_NUMBER}"
        K8S_NAMESPACE = "default"
    }

    stages {

        stage('Git Checkout') {   // Unique name
            steps {
                git branch: 'main',
                    url: 'https://github.com/hemanthpb19/Java-Project-',
                    credentialsId: 'github-creds'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    sh """
                        docker build -t ${DOCKER_REGISTRY}/${IMAGE_NAME}:${DOCKER_TAG} .
                        docker push ${DOCKER_REGISTRY}/${IMAGE_NAME}:${DOCKER_TAG}
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                        kubectl set image deployment/${IMAGE_NAME} ${IMAGE_NAME}=${DOCKER_REGISTRY}/${IMAGE_NAME}:${DOCKER_TAG} -n ${K8S_NAMESPACE}
                        kubectl rollout status deployment/${IMAGE_NAME} -n ${K8S_NAMESPACE}
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}

