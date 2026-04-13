pipeline {
    agent any 
    environment {
        NEXUS_REGISTRY = "192.168.81.131:30010"
        REPO_NAME = "alumni-app"
        IMAGE_NAME = "notification-service"
        VERSION = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                // This now works because the job is linked to the Git Repo
                checkout scm 
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'nexus-creds', 
                                     passwordVariable: 'NEXUS_PWD', 
                                     usernameVariable: 'NEXUS_USER')]) {
                        sh "echo ${NEXUS_PWD} | docker login -u ${NEXUS_USER} --password-stdin ${NEXUS_REGISTRY}"
                    }
                }
            }
        }
        stage('Build & Push') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${VERSION} ."
                sh "docker tag ${IMAGE_NAME}:${VERSION} ${NEXUS_REGISTRY}/${REPO_NAME}/${IMAGE_NAME}:${VERSION}"
                sh "docker push ${NEXUS_REGISTRY}/${REPO_NAME}/${IMAGE_NAME}:${VERSION}"
            }
        }
        stage('Deploy to K8s (Mock)') {
            steps {
                echo "Skipping K8s deployment for local testing."
                echo "Image is ready in Nexus: ${NEXUS_REGISTRY}/${REPO_NAME}/${IMAGE_NAME}:${VERSION}"
                // sh "kubectl set image ..." // Commented out until college setup
            }
        }
    }
}
