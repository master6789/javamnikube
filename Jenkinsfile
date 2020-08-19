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
            withKubeConfig([credentialsId: 'dc8af005-5efa-45b7-b9ec-caab715764bd']) {
                sh 'kubectl create -f deployment.yml'
            }
        }
      }
    }
  }
}
