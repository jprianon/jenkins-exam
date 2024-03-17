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
                    docker build -t jprianon/jenkins-exam-cast-service ./Jenkins-exam/cast-service
                    docker build -t jprianon/jenkins-exam-movie-service ./Jenkins-exam/movie-service
                    '''
                    }
                }
            }
        
        stage('Docker Push'){ 
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

        stage('Deploy to Dev') {
            environment {
                KUBECONFIG = credentials("config")
            }
                steps {
                    script {
                    sh '''
                    rm -Rf .kube
                    mkdir .kube
                    ls
                    cat $KUBECONFIG > .kube/config
                    helm upgrade --install ms-fastapi  helmchart --namespace dev --values ms-fastapi-chart/values-dev.yaml
                    kubectl get deploy,svc,Pod -n dev
                    '''
                    }
                }
        }

        stage('Deploy to QA') {
            environment {
                KUBECONFIG = credentials("config")
            }
                steps {
                    script {
                    sh '''
                    rm -Rf .kube
                    mkdir .kube
                    ls
                    cat $KUBECONFIG > .kube/config
                    helm upgrade --install ms-fastapi  helmchart --namespace qa --values ms-fastapi-chart/values-qa.yaml
                    kubectl get deploy,svc,Pod -n qa
                    '''
                    }
                }
        }

        stage('Deploy to Staging') {
            environment {
                KUBECONFIG = credentials("config")
            }
                steps {
                    script {
                    sh '''
                    rm -Rf .kube
                    mkdir .kube
                    ls
                    cat $KUBECONFIG > .kube/config
                    helm upgrade --install ms-fastapi  helmchart --namespace staging --values ms-fastapi-chart/values-staging.yaml
                    kubectl get deploy,svc,Pod -n staging
                    '''
                    }
                }
        }

        stage('Manual Deployment to Production') {
            when {
                expression {
                    env.GIT_BRANCH == "origin/master"
                }
            }
            steps {
                input message: 'Deploy to prod environment ?', ok: 'yes'
                script {
                    sh '''
                    rm -Rf .kube
                    mkdir .kube
                    ls
                    cat $KUBECONFIG > .kube/config
                    helm upgrade --install ms-fastapi  helmchart --namespace staging --values ms-fastapi-chart/values-staging.yaml
                    kubectl get deploy,svc,Pod -n staging #
                    '''
                }
            }
        }
    }
}
