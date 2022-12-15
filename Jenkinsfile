pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="230028365112"
        AWS_DEFAULT_REGION="us-east-1"
        IMAGE_REPO_NAME="frontend"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "230028365112.dkr.ecr.us-east-1.amazonaws.com/frontend"
        AWS_ACCESS_KEY_ID=credentials('Venkatesh-aws access-key-id')
        AWS_SECRET_ACCESS_KEY=credentials('Venkatesh-aws-secret-key')
    }
    stages {
        stage('Cloning Git') {
            steps {
                cleanWs()
                checkout([$class: 'GitSCM', branches: [[name:'*/develop']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'daiva-github-credentials', url: 'https://github.com/daivakrupa/ems-angular.git']]]) 
                
            }
            
        }
        
        //stage('NPM Install') {
        //  steps{ 
        //       sh """
        //       rm -rf node_modules
        //       npm install
        //       pwd
        //       npm run build
        //       """
        //   }
        //}
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
                    sh "AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID} AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY} aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                }
                 
            }
        }
        
        stage('Pushing to ECR') {
            steps{  
                script {
                    sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                    sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                    sh "docker images -q ${IMAGE_REPO_NAME}:${IMAGE_TAG} | xargs docker rmi -f"
                }
            }
        }
    }
}
