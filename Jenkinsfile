pipeline {
  environment {
    dockerimagename = "aydispa/react-app"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/aymeric-dispa/k8s-deploy.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Pushing Image') {
      environment {
          registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Apply Kubernetes files') {
        steps {
          sh 'kubectl apply -f deployment.yaml'
          sh 'kubectl apply -f service.yaml'
          }
    }
  }
}