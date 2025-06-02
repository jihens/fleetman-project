pipeline {
       agent any
       environment {
           KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'
           KUBECONFIG = "/var/jenkins_home/.kube/config"
           DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
           IMAGE_NAME = "jihene/webapp"
           IMAGE_TAG = "test-${BUILD_NUMBER}"
       }
       stages {
           stage('Preparation') {
               steps {
                   checkout scm
                   echo 'Code source du webapp récupéré'
               }
           }
           stage('Build Docker Image') {
               steps {
                   echo 'Construction de l\'image Docker pour webapp...'
                   sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
               }
           }
           stage('Push Docker Image') {
               steps {
                   echo 'Poussée de l\'image Docker vers Docker Hub...'
                   sh 'echo $DOCKER_HUB_CREDENTIALS_PSW | docker login -u $DOCKER_HUB_CREDENTIALS_USR --password-stdin'
                   sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
               }
           }
           stage('Deploy') {
               steps {
                   echo 'Déploiement du webapp sur Minikube...'
                   sh "sed -i 's|image: .*|image: ${IMAGE_NAME}:${IMAGE_TAG}|' ${WORKSPACE}/manifests/webapp.yaml"
                   sh "kubectl apply -f ${WORKSPACE}/manifests/webapp.yaml"
                   sh "kubectl get all"
                   echo 'Déploiement du webapp terminé'
               }
           }
       }
       post {
           failure {
               echo 'Pipeline échoué pour webapp. Vérifie les logs pour plus de détails.'
           }
       }
   }
