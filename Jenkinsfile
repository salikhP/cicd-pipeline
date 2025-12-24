pipeline {
	agent any

	environment {
		IMAGE_NAME = "b1ame/cicd-pipeline"
		DOCKERHUB_CREDS_ID = "dockerhub_id"
	}

	stages {
		stage('Git Checkout') {
			steps {
				checkout scm
			}
		}

		stage('Application Build') {
			steps {
				sh 'chmod +x scripts/build.sh'
				sh 'script scripts/build.sh'
			}
		}

		stage('Tests') {
			steps {
				sh 'chmod +x scripts/test.sh'
				sh 'script scripts/test.sh'
			}
		}

		stage('Docker Image Build') {
			steps {
				sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} -t ${IMAGE_NAME}:latest .'
			}
		}

		stage('Docker Image Push') {
			steps {
				withCredentials([
					usernamePassword(
						credentialsId: "${DOCKERHUB_CREDS_ID}",
						usernameVariable: 'DOCKER_USER',
						passwordVariable: 'DOCKER_PASS'
					)
				]) {
					sh '''
						echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
						docker push ${IMAGE_NAME}:${BUILD_NUMBER}
						docker push ${IMAGE_NAME}:latest
					'''
				}
			}
		}
	}
}