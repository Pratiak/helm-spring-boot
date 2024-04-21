pipeline {
    agent none
    
    environment {
        DOCKERHUB_CREDENTIALS= credentials('dockerhubcredentials')
        IMAGE_NAME = 'helm-app'
        IMAGE_TAG = "$BUILD_NUMBER"
        DOCKER_USERNAME = 'yash545'
    }

    stages {
        stage('git checkout') {
            agent {
                label 'Docker'
            }
            steps {
                git branch: 'main', url: 'https://github.com/Yashwinrajs/helm-spring-boot.git'
            }
        }
        stage('image build') {
            agent {
                label 'Docker'
            }
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }
        stage('image push') {
            agent {
                label 'Docker'
            }
            steps {
                script {
                    sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW> --password-stdin'
                    sh 'docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}'
                    sh 'docker push ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}'
                    sh 'docker logout'
                }
            }
        }
        stage('helm deploy') {
            agent {
                label 'Kubernetes'
            }
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/Yashwinrajs/helm-spring-boot.git'
                    sh 'helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER' 
                }
            }
        }
    }
}
