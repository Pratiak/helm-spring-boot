pipeline{
    agent any
    
    environment{
        DOCKERHUB_CREDENTIALS=credentials('dockerid')
        IMAGE_TAG="$BUILD_NUMBER"
        IMAGE_NAME='spring'
        DOCKER_USERNAME='pratiak'
    }
    
    stages{
        stage('git checkout'){
             agent{
           label 'docker' 
        } 
            steps{
                 git branch: 'main', url: 'https://github.com/Pratiak/helm-spring-boot.git'
              }
    }
    
    stage('docker build'){
        agent{
           label 'docker' 
        } 
        steps{
           sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
        }
    }
    
    stage('docker push'){
        agent{
            label 'docker'
        }
        steps{
             script {
                    sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW> --password-stdin'
                    sh 'docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}'
                    sh 'docker push ${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}'
                    sh 'docker logout'
                }
        }
        }
        
    stage('Deploy to kubernetes'){
        agent{
            label 'kubernetes'
        }
        
        steps{
            script{
                git branch: 'main', url: 'https://github.com/Pratiak/helm-spring-boot.git'
                sh 'helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER'
            }
        }
    }
    }

}
