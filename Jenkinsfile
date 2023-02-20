pipeline {
    agent any
    environment {
        registry = "public.ecr.aws/e5k2g5i2/e-plansoftdemoimage"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/Osomudeya/jenkins-ecr-eks.git']]])     
            }
        }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry 
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr-public get-login-password --region us-east-2 | docker login --username AWS --password-stdin public.ecr.aws/e5k2g5i2'
                sh 'docker push public.ecr.aws/e5k2g5i2/e-plansoftdemoimage:latest'
         }
        }
      }

       stage('K8S Deploy') {
        steps{   
            script {
                withKubeConfig([credentialsId: 'K8S', serverUrl: '']) {
                sh ('kubectl apply -f  deployment.yaml')
                }
            }
        }
       }
    }
}
