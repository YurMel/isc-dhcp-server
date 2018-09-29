  environment {
    registry = "docker_hub_account/repository_name"
    registryCredential = 'DockerHub'
  }
  agent any
  stages {
    stage('Building image') {
      steps{
        script {
          docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh 'docker push shanem/spring-petclinic:latest'
        }
      }
    }
    stage("Publish") {
      withDockerRegistry([credentialsId: 'DockerHub']) {
        sh "docker push ${DOCKERHUB_USERNAME}/isc-dhcp-server:${BUILD_NUMBER}"
      }
    }
  }
