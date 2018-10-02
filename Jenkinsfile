env.DOCKERHUB_USERNAME = 'yurmel'
env.DOCKERHUB_IMAGE = 'mysql'
env.DOCKERHUB_REPO = '${DOCKERHUB_USERNAME}/${DOCKERHUB_IMAGE}'

node('node1.devops.ua') {
    checkout scm

//    stage("Unit Test") {
//	sh "docker run --rm -v ${WORKSPACE}:/go/src/docker-ci-cd golang go test docker-ci-cd -v --run Unit"
//    }
    stage("Integration Test") {
	try {
	    sh "docker build -t mysql ."
	    sh "docker rm -f mysql || true"
	    sh "docker run -d  -p 3306:3306 --name=mysql mysql"
	}
	catch(err) {
	    error "Integration Test failed"
	}
	finally {
	    sh "docker rm -f mysql || true"
	    sh "docker ps -aq | xargs docker rm || true"
	    sh "docker images -aq -f dangling=true | xargs docker rmi || true"
	}
    }
    stage("Build") {
	sh "docker build -t ${DOCKERHUB_USERNAME}/mysql:${BUILD_NUMBER} ."
	docker.build DOCKERHUB_REPO + ":$BUILD_NUMBER"
    }
    stage("Publish") {
	withDockerRegistry([credentialsId: 'DockerHub']) {
	    sh "docker push ${DOCKERHUB_USERNAME}/mysql:${BUILD_NUMBER}"
	}
    }
/*
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
*/
}
