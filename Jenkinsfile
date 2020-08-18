pipeline {
  environment {
    registry = "42e35e61f3/demo"
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
    stage('Deploy to Kubernetes') {
        steps {
            sh 'kubectl apply -f deployment.yml'
       }
    }
  }
}
