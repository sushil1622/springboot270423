pipeline {
    tools {
        maven 'Maven3'
    }
    
    agent any
    
    environment {
        registry = "159308473286.dkr.ecr.ap-south-1.amazonaws.com/blue-cloud"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/sushil1622/springboot270423.git']]])
            }
        }
        
        stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
        }
        
        stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry 
        }
      }
    }
    
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 159308473286.dkr.ecr.ap-south-1.amazonaws.com'
                sh 'docker push 159308473286.dkr.ecr.ap-south-1.amazonaws.com/blue-cloud:latest'
         }
        }
      }
    
    stage('K8S Deploy') {
        steps{   
            script {
                withKubeConfig([credentialsId: 'kubernetes', serverUrl: '']) {
                sh ('kubectl apply -f  eks-deploy-k8s.yaml')
                }
            }
        }
       }
       
    }
}