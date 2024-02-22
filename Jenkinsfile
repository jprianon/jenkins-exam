pipeline {
    agent any
    
    parameters {
        choice(name: 'ENVIRONNEMENT', choices: ['dev', 'QA', 'staging', 'prod'], description: 'Sélectionnez l\'environnement cible.')
    }
    
    stages {
        stage('Clonage du dépôt') {
            steps {
                git branch: 'master', url: 'https://github.com/votre-utilisateur/votre-projet.git'
            }
        }
        
        stage('Construction de l\'application') {
            steps {
                sh 'pip install -r requirements.txt' // Remplacez cette commande par celle correspondant à votre processus de construction
            }
        }
        
        //stage('Tests de l\'application') {
        //    steps {
        //        sh 'mvn test' // Remplacez cette commande par celle correspondant à vos tests
        //    }
        //}
        
        stage('Construction de l\'image Docker') {
            when {
                expression { params.ENVIRONNEMENT == 'prod' }
            }
            steps {
                script {
                    docker.build('jprianon/jenkins-exam') // Remplacez cette commande par la construction de votre image Docker
                    docker.withRegistry('https://registry.hub.docker.com', 'credentials-id') {
                        docker.image('jprianon/jenkins-exam').push('latest')
                    }
                }
            }
        }
        
        stage('Déploiement sur Kubernetes') {
            when {
                expression { params.ENVIRONNEMENT != 'prod' }
            }
            steps {
                sh "kubectl apply -f manifests/${params.ENVIRONNEMENT}/"
            }
        }
    }
    
    post {
        success {
            echo 'Le déploiement est réussi!'
        }
        failure {
            echo 'Le déploiement a échoué. Veuillez vérifier les logs pour plus de détails.'
        }
    }
}