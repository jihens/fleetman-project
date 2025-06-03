pipeline {
         agent {
             kubernetes {
                 yaml '''
                 apiVersion: v1
                 kind: Pod
                 spec:
                   containers:
                   - name: kaniko
                     image: gcr.io/kaniko-project/executor:latest
                     command:
                     - sleep
                     args:
                     - "9999999"
                     volumeMounts:
                     - name: docker-config
                       mountPath: /kaniko/.docker
                   volumes:
                   - name: docker-config
                     secret:
                       secretName: docker-credentials
                 '''
             }
         }
         environment {
             KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'
             KUBECONFIG = "/var/jenkins_home/.kube/config"
             DOCKER_HUB_CREDENTIALS = credentials('dockerhub-credentials')
             IMAGE_NAME = "jihenefarhani/webapp"
             IMAGE_TAG = "test-${BUILD_NUMBER}"
         }
         stages {
             stage('Fetch Code') {
                 steps {
                     echo 'Récupération du code source depuis GitHub...'
                     git url: "https://github.com/jihens/fleetman-project.git", branch: "webapp-test"
                 }
             }
             stage('Build and Push with Kaniko') {
                 steps {
                     container('kaniko') {
                         sh '''
                         /kaniko/executor \
                             --context=dir://$WORKSPACE \
                             --dockerfile=$WORKSPACE/Dockerfile \
                             --destination=$IMAGE_NAME:$IMAGE_TAG \
                             --build-arg=DOCKER_USERNAME=$DOCKER_HUB_CREDENTIALS_USR \
                             --build-arg=DOCKER_PASSWORD=$DOCKER_HUB_CREDENTIALS_PSW
                         '''
                     }
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
