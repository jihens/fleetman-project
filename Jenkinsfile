pipeline {
       agent any
       environment {
           KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'
           KUBECONFIG = "/var/jenkins_home/.kube/config"  // Utilise le kubeconfig monté
       }
       stages {
           stage('Preparation') {
               steps {
                   checkout scm
                   echo 'Code source du webapp récupéré'
               }
           }
           stage('Deploy') {
               steps {
                   echo 'Déploiement du webapp sur Minikube...'
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
