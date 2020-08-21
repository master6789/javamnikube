pipeline {
  environment {
    registry = "master6789/demo"
    registryCredential = 'dockerhub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Compile') {
      steps {
        git 'https://github.com/master6789/javamnikube.git'
        script {
                def mvnHome = tool name: 'MAVEN_HOME', type: 'maven'
                sh "${mvnHome}/bin/mvn package"
        }
      }
    }
    stage('Building Docker Image') {
      steps {
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image To Docker Hub') {
      steps {
        script {
          /* Finally, we'll push the image with two tags:
                   * First, the incremental build number from Jenkins
                   * Second, the 'latest' tag.
                   * Pushing multiple tags is cheap, as all the layers are reused. */
              docker.withRegistry('https://registry.hub.docker.com','dockerhub') {
                dockerImage.push("${env.BUILD_NUMBER}")
                dockerImage.push("latest")
          }
        }
      }
    }
    stage('Apply Kubernetes files') {
      steps {
        script {
           withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'minikube', contextName: 'minikube', credentialsId: '6b78785c-ac66-4d3e-9066-5f57f971284d', namespace: 'default', serverUrl: 'https://192.168.99.127:8443']]) {
                sh 'kubectl create -f deployment.yml'
            }
        }
      }
    }
  }
}
