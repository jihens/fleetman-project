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
                echo 'Code source récupéré'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Déploiement sur Minikube...'
                sh "kubectl apply -f ${WORKSPACE}/manifests/webapp.yaml"
                sh "kubectl get all"
                echo 'Déploiement terminé'
            }
        }
    }
    post {
        failure {
            echo 'Pipeline échoué. Vérifie les logs pour plus de détails.'
        }
    }
}
