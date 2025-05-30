pipeline {
    agent {
        docker {
            image 'bitnami/kubectl:latest'  // Image avec kubectl
            args '--entrypoint='  // Permettre les commandes shell
        }
    }
    environment {
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'  // ID du kubeconfig
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
                withKubeConfig([credentialsId: KUBECONFIG_CREDENTIALS_ID]) {
                    echo 'Déploiement sur Minikube...'
                    sh "kubectl apply -f ${WORKSPACE}/manifests/webapp.yaml"
                    sh "kubectl get all"
                    echo 'Déploiement terminé'
                }
            }
        }
    }
    post {
        failure {
            echo 'Pipeline échoué. Vérifie les logs pour plus de détails.'
        }
    }
}
