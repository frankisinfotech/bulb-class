pipeline {
    agent any
    
    environment {
        SERVICE_NAME = "bulbclass"
        ORGANIZATION_NAME = "frankisinfotech"
        DOCKERHUB_USERNAME = "essiendaniel33"
        REPOSITORY_TAG = "${DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
    }
   
    stages {
            
            
        stage ('Build and Push Image') {
            steps {
                 withDockerRegistry([credentialsId: 'docker-hub', url: ""]) {
                   sh 'docker build -t ${REPOSITORY_TAG} .'
                   sh 'docker push ${REPOSITORY_TAG}'          
            }
          }
       }
        stage("Install kubectl"){
            steps {
                sh """
                    curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
                    chmod +x ./kubectl
                    ./kubectl version --client
                """
            }
        }
        
        stage ('Deploy to Cluster') {
            steps {
                //withAWS(role: "Jenkins", roleAccount: '164135465533') {
                sh "aws eks update-kubeconfig --region us-east-1 --name essien-eks"
               // sh "aws eks update-kubeconfig --region us-east-1 --name essien-eks"
                sh " envsubst < ${WORKSPACE}/deploy.yaml | ./kubectl apply -f - "
                //}
            }
        }
    }
}
