env.DOCKERHUB_USERNAME = 'YurMel'

node("production") {
    checkout scm

    stage("Unit Test") {
	sh "docker run --rm -v ${WORKSPACE}:/go/src/docker-ci-cd golang go test docker-ci-cd -v --run Unit"
    }
    stage("Integration Test") {
	try {
	    sh "docker build -t docker-ci-cd ."
	    sh "docker rm -f docker-ci-cd || true"
	    sh "docker run -d  -p 9098:8080 --name=docker-ci-cd docker-ci-cd"
	    // env variable is used to set the server where go test will connect to run the test
	    sh "docker run --rm -v ${WORKSPACE}:/go/src/docker-ci-cd --link=docker-ci-cd -e SERVER=docker-ci-cd golang go test docker-ci-cd -v --run Integration"
	}
	catch(e) {
	    error "Integration Test failed"
	}finally {
	    sh "docker rm -f docker-ci-cd || true"
	    sh "docker ps -aq | xargs docker rm || true"
	    sh "docker images -aq -f dangling=true | xargs docker rmi || true"
	}
    }
    stage("Build") {
	sh "docker build -t ${DOCKERHUB_USERNAME}/docker-ci-cd:${BUILD_NUMBER} ."
    }
    stage("Publish") {
	withDockerRegistry([credentialsId: 'DockerHub']) {
	sh "docker push ${DOCKERHUB_USERNAME}/docker-ci-cd:${BUILD_NUMBER}"
    }

}