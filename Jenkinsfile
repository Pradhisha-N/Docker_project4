pipeline{
	agent any
	environment{
		DOCKER_IMAGE = "pradhisha/my-jenkins-app"
		DOCKER_CREDENTIALS_ID = "docker-hub-credentials"

	}
	stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning repository..."
                git url: 'https://github.com/Pradhisha-N/Docker_project4.git', branch: 'main'
                echo "Repository cloned successfully."
            }
        }

        stage('Build Docker Image') {
    steps {
        script {
            echo "Logging into Docker Hub..."
            withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker build -t ${DOCKER_IMAGE} .
                '''
            }
            echo "Docker image built successfully."
        }
    }
}


	stage('Run Container Locally'){
		steps {
			script {
				sh "docker run -d -p 8050:80 --name jenkins-docker ${DOCKER_IMAGE}:latest"
			}
		}
}

        stage('Push Docker Image') {
            steps {
                script {
                    echo "Logging into Docker Hub..."
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        echo "Pushing Docker image..."
                        sh "docker push ${DOCKER_IMAGE}"
                        echo "Docker image pushed successfully."
                    }
                }
            }

        }
        stage('Deploy to server') {
            steps {
                script {
withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
sh '''
echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
docker pull pradhisha/my-jenkins-app:latest
'''


}

                }
            }
        }

    }
}
