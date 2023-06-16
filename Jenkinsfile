pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="315146163818"
        AWS_DEFAULT_REGION="ap-south-1"
        IMAGE_REPO_NAME="springboot_project"
        IMAGE_TAG="latest"
        REPOSITORY_URI= "315146163818.dkr.ecr.ap-south-1.amazonaws.com/springboot_project"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                git credentialsId: 'cena-codecommit', url: 'https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/application_pipeline_testing'
            }
        }
  
        // Building Docker images
        stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
        stage('Logging into AWS ECR') {
            steps {
                script {
                    sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
                }
            }
        }
        
        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps{  
                script {
                    sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                    sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
                }
            }
        }
        
        stage('Remote SSH') {
            steps {
                script {
                    def remote = [
                         name: 'ec2-13-233-164-92.ap-south-1.compute.amazonaws.com',
                         port: 50033,
                         host: '13.234.22.22',
                         user: '1CHAdministrator',
                         password: 'admin',
	                     sudo: true,
                         allowAnyHosts: true
                    ]
                    sshCommand remote: remote, command: "sudo minikube version"
                    sshCommand remote: remote, command: "sudo minikube status"
                    sshCommand remote: remote, command: "git clone "
                }
            }
        }
    }
}