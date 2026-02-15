pipeline {
    agent any

    tools {
        maven 'maven'  // Name of Maven in Jenkins Global Tool Configuration
        jdk 'jdk17'    // Name of JDK in Jenkins Global Tool Configuration
    }

    environment {
        IMAGE_NAME = "devops-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/hemanthpb19/Java-Project-'  // Replace with your Git repo URL
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar') {  // 'sonar' = SonarQube server configured in Jenkins
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build') {
            steps {
                // Build Docker image inside Minikube so Kubernetes can use it
                sh '''
                eval $(minikube docker-env)
                docker build -t devops-app:latest .
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s/
                kubectl get pods
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

