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
    stage('Snyk code test') {
      steps {
        echo 'Checking code security'
               script {
                   withCredentials([string(credentialsId: 'snyk-token-text', variable: 'SNYK_TOKEN')]) {
                       sh '/var/jenkins_home/tools/io.snyk.jenkins.tools.SnykInstallation/snyk_arm64/snyk-alpine test --token=$SNYK_TOKEN --severity-threshold=critical'
                   }
               }
      }
    }
    stage('Build image') {
      steps {
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Snyk container test') {
      steps {
        echo 'Checking container security'
               script {
                   withCredentials([string(credentialsId: 'snyk-token-text', variable: 'SNYK_TOKEN')]) {
                       sh '/var/jenkins_home/tools/io.snyk.jenkins.tools.SnykInstallation/snyk_arm64/snyk-alpine test container --print-deps --json --file=Dockerfile --token=$SNYK_TOKEN'
                   }
               }
      }
    }
    stage('Pushing Image') {
      environment {
        registryCredential = 'dockerhub-credentials'
      }
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
            dockerImage.push("latest")
            dockerImage.push("1.0.1")
          }
        }
      }
    }
//     stage('Apply Kubernetes files') {
//       steps {
//         withKubeConfig([credentialsId: 'jenkins-token', serverUrl: 'https://host.docker.internal:50079']) {
//           sh '''
//             KUBECTL_PATH=$HOME/bin
//             mkdir -p $KUBECTL_PATH
//             if ! command -v kubectl >/dev/null 2>&1; then
//               echo "Installing kubectl..."
//               curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
//               chmod +x kubectl
//               mv kubectl $KUBECTL_PATH/kubectl
//             else
//               echo "kubectl already installed"
//             fi
//             export PATH=$KUBECTL_PATH:$PATH
//             kubectl version --client
//             kubectl apply -f deployment.yaml --validate=false
//             kubectl apply -f service.yaml --validate=false
//           '''
//         }
//       }
//     }
  }
}