pipeline {
    environment {
        registry = "mjagiela/jenkins-docker-test"
        DOCKER_PWD = credentials('docker-login-pwd')
    }
    agent {
        docker {
            image 'mjagiela/node-docker'
            args '-p 3000:3000'
            args '-w /app'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage("Build"){
            steps {
                sh 'npm install'
            }
        }
        stage("Test"){
            steps {
                sh 'npm test'
            }
        }
        stage("Build & Push Docker image") {
            steps {
                sh 'echo "Build"'
                sh 'docker image build -t $registry:$BUILD_NUMBER .'
                sh 'echo "login"'
                sh 'docker login -u mjagiela -p $DOCKER_PWD'
                sh 'echo "push"'
                sh 'docker image push $registry:$BUILD_NUMBER'
                sh 'echo "remove"'
                sh "docker image rm $registry:$BUILD_NUMBER"
            }
        }
	stage('Deployt and smoke test') {
	     steps {
		sh './jenkins/scripts/deploy.sh'	
	     }
	}	
	stage('Cleanup') {
	    steps{
		sh './jenkins/scripts/cleanup.sh'
            }
	}
    }
}

