pipeline {
  environment {
    registry = "docker_hub_account/repository_name"
    registryCredential = 'dockerhub'
  }
  agent any

  node("env_stagging") {
    checkout scm

    stages {
      stage('Building image') {
        steps{
          script {
            docker.build registry + ":$BUILD_NUMBER"
          }
        }
      }
    }
  }
}
