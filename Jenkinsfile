def commit_id = ''

pipeline {
	agent any

	stages {
		stage('Preparation') {
			steps {
				checkout scm
				sh 'git rev-parse --short HEAD > commit-id.txt'
				script {
					commit_id = readFile('commit-id.txt').trim()
					echo "Commit ID: ${commit_id}"
				}
			}
		}

		stage('Deploy') {
			steps {
				echo 'Preparing Kubernetes deployment...'
				sh "sed -i 's/commit_id/${commit_id}/g' ./manifests/webapp.yaml"
				sh "kubectl get all"
				sh "kubectl apply -f ./manifests/"
				echo '✅ Deployment complete'
			}
		}
	}
}
