pipeline {
    agent any
    
    environment {
        DOCKER_CREDENTIALS = credentials('master')
        KUBECONFIG = credentials('kubeconfig-id')
        CHART_VERSION = '1.0.0'
    }

    stages {
        stage('Checkout') {
            steps {
                // Récupérer le code depuis le dépôt GitHub
                git credentialsId: 'master', url: 'https://github.com/jprianon/jenkins-exam.git'
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                // Construire et pousser l'image Docker vers DockerHub
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        docker.build("jprianon/jenkins-exam:${env.CHART_VERSION}")
                        docker.image("jprianon/jenkins-exam:${env.CHART_VERSION}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Déployer l'application sur Kubernetes en utilisant Helm
                withCredentials([file(credentialsId: 'kubeconfig-id', variable: 'KUBECONFIG')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG
                        helm upgrade --install --namespace dev --version ${env.CHART_VERSION} nom-de-votre-release ./charts
                    '''
                }
            }
        }

        stage('Manual Deployment to Production') {
            when {
                branch 'master'
            }
            steps {
                input "Confirmez-vous le déploiement en environnement de production ?"
                // Déployer manuellement en production
                withCredentials([file(credentialsId: 'kubeconfig-id', variable: 'KUBECONFIG')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG
                        helm upgrade --install --namespace prod --version ${env.CHART_VERSION} nom-de-votre-release ./charts
                    '''
                }
            }
        }
    }
}