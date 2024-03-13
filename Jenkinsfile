pipeline {
    agent any
    
    environment {
        DOCKER_ID = "jprianon"
        DOCKER_IMAGE = "jenkins-exam"
        DOCKER_TAG = "v.${BUILD_ID}.0"
        KUBECONFIG = "/home/ubuntu/.kube/config"
        CHART_VERSION = '1.0.0'
    }
    stages {
        stage('Docker Build'){
                steps {
                    script {
                    sh '''
                    docker build -t jprianon/jenkins-exam-cast-service ./cast-service
                    docker build -t jprianon/jenkins-exam-movie-service ./movie-service
                    '''
                    }
                }
            }
        
        stage('Docker Push'){ //we pass the built image to our docker hub accountttt
            //environment
            //{
            //    DOCKER_PASS = credentials("DOCKER_HUB_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
            //}

            steps {

                script {
                sh '''
                docker login -u $DOCKER_ID -p dckr_pat_KdmVj2VyGObjtG6RE-rvJqinXb0
                docker tag jenkins-exam_cast_service jprianon/jenkins-exam-cast-service
                docker tag jenkins-exam_movie_service jprianon/jenkins-exam-movie-service
                docker push jprianon/jenkins-exam-cast-service:latest
                docker push jprianon/jenkins-exam-movie-service:latest
                '''
                }
            }

        }

        stage('Deploy to Kubernetes-dev') {
             environment
            {
            KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins !
            }
                steps {
                    script {
                    sh '''
                    #rm -Rf .kube
                    #mkdir .kube
                    #ls
                    #cat $KUBECONFIG > .kube/config
                    helm upgrade --install ms-fastapi-dev  ms-fastapi-chart/dev --namespace dev --values=./ms-fastapi-chart/dev/values.yaml
                    helm ls
                    kubectl get deploy,svc,Pod
                    #helm upgrade --install movie-cast-app helm-chart/ --namespace prod --set image.tag=${BUILD_NUMBER}'
                    #kubectl --kubeconfig=$KUBECONFIG apply -f manifests/dev.yaml --namespace=$NAMESPACE_DEV'
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
                // Déployer manuellement en production !!
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
///
//
//pipeline {
//environment { // Declaration of environment variables
//DOCKER_ID = "jprianon" // replace this with your docker-id
//DOCKER_IMAGE = "jenkins-exam"
//DOCKER_TAG = "v.${BUILD_ID}.0" // we will tag our images with the current build in order to increment the value by 1 with each new build
//}
//agent any // Jenkins will be able to select all available agents
//stages {
//        stage(' Docker Build'){ // docker build image stage
//            steps {
//                script {
//                sh '''
//                 docker rm -f jenkins
//                 docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
//                sleep 6
//                '''
//                }
//            }
//        }
//        stage('Docker run'){ // run container from our builded image
//                steps {
//                    script {
//                    sh '''
//                    docker run -d -p 80:80 --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
//                    sleep 10
//                    '''
//                    }
//                }
//            }
//
//        stage('Test Acceptance'){ // we launch the curl command to validate that the container responds to the request
//            steps {
//                    script {
//                    sh '''
//                    curl localhost
//                    '''
//                    }
//            }
//
//        }
//        stage('Docker Push'){ //we pass the built image to our docker hub account
//            environment
//            {
//                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
//            }
//
//            steps {
//
//                script {
//                sh '''
//                docker login -u $DOCKER_ID -p $DOCKER_PASS
//                docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
//                '''
//                }
//            }
//
//        }
//
//stage('Deploiement en dev'){
//        environment
//        {
//        KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
//        }
//            steps {
//                script {
//                sh '''
//                rm -Rf .kube
//                mkdir .kube
//                ls
//                cat $KUBECONFIG > .kube/config
//                cp fastapi/values.yaml values.yml
//                cat values.yml
//                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
//                helm upgrade --install app fastapi --values=values.yml --namespace dev
//                '''
//                }
//            }
//
//        }
//stage('Deploiement en staging'){
//        environment
//        {
//        KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
//        }
//            steps {
//                script {
//                sh '''
//                rm -Rf .kube
//                mkdir .kube
//                ls
//                cat $KUBECONFIG > .kube/config
//                cp fastapi/values.yaml values.yml
//                cat values.yml
//                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
//                helm upgrade --install app fastapi --values=values.yml --namespace staging
//                '''
//                }
//            }
//
//        }
//  stage('Deploiement en prod'){
//        environment
//        {
//        KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
//        }
//            steps {
//            // Create an Approval Button with a timeout of 15minutes.
//            // this require a manuel validation in order to deploy on production environment
//                    timeout(time: 15, unit: "MINUTES") {
//                        input message: 'Do you want to deploy in production ?', ok: 'Yes'
//                    }
//
//                script {
//                sh '''
//                rm -Rf .kube
//                mkdir .kube
//                ls
//                cat $KUBECONFIG > .kube/config
//                cp fastapi/values.yaml values.yml
//                cat values.yml
//                sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
//                helm upgrade --install app fastapi --values=values.yml --namespace prod
//                '''
//                }
//            }
//
//        }
//
//}
//}