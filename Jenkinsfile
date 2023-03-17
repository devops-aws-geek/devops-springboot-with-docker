pipeline {
    agent any
	environment {
        // Get the credentials stored in jenkins Credential
        GIT_CREDENTIALS = credentials('GIT_HUB_CREDENTIAL_K8S_PIPELINE')
		DOCKER_HUB_PASSWORD = credentials('DOCKER_HUB_PASSWORD')
    }
    stages{
    stage("Git Clone"){
        steps {
        git credentialsId: $GIT_CREDENTIALS, url: 'https://github.com/devops-aws-geek/devops-springboot-with-docker.git'
		}
    }

    stage('Gradle Build') {
       steps {
       sh './gradlew build'
    }
    }

    stage("Docker build"){
	    steps {
        sh 'docker version'
        sh 'docker build -t devopswithdeepak-docker-demo .'
        sh 'docker image list'
        sh 'docker tag devopswithdeepak-docker-demo deepak2717/devopswithdeepak-docker-demo:devopswithdeepak-docker-demo'
		
    }
	}
    satge("Docker Login") {
    steps {
        sh 'docker login -u rahulwagh17 -p $DOCKER_HUB_PASSWORD'
    }
	}

    stage("Push Image to Docker Hub"){
        sh 'docker push  deepak2717/devopswithdeepak-docker-demo:devopswithdeepak-docker-demo'
    }

    stage("SSH Into k8s Server and deploy") {
	    steps {
		script{
        def remote = [:]
        remote.name = 'K8S master'
        remote.host = '100.0.0.3'
        remote.user = 'vagrant'
        remote.password = 'vagrant'
        remote.allowAnyHosts = true
        sshPut remote: remote, from: 'k8s-spring-boot-deployment.yml', into: '.'
        sshCommand remote: remote, command: "kubectl apply -f k8s-spring-boot-deployment.yml"
        }
    }
	}
 }
}