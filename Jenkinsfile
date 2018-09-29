node {
    checkout scm

    stage('Building image') {
      docker.build registry + ":$BUILD_NUMBER"
    }
    stage('Docker Push') {
      withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
        sh 'docker push shanem/spring-petclinic:latest'
      }
    }
    stage("Publish") {
      withDockerRegistry([credentialsId: 'DockerHub']) {
        sh "docker push ${DOCKERHUB_USERNAME}/isc-dhcp-server:${BUILD_NUMBER}"
      }
    }
}
