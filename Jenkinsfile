// pipeline {
   agent any
   environment {
      PROJECT = 'WELCOME TO eks-cluster-01 BATCH - Jenkins Class'
      AWS_REGION = 'us-east-2'
      CLUSTER_NAME = 'eks-cluster-01'
   }
   stages {
      stage('Check The Kubernetes Access') {
         steps {
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', 
                              credentialsId: 'aws-credentials-id']]) {
                sh '''
                aws eks --region ${AWS_REGION} update-kubeconfig --name ${CLUSTER_NAME}
                kubectl get pods -A || exit 1
                kubectl get ns || exit 1
                '''
            }
         }
      }
      stage('Deploy Voting App') {
         steps {
            sh '''
            ls -al
            kubectl apply -f voting-with-ingress.yml || exit 1
            kubectl apply -f ingress.yml || exit 1
            kubectl apply -f ingress-nk.yml || exit 1
            kubectl get pods,svc,ingress || exit 1
            '''
         }
      }
      stage('Deploy Ingress for Vote & Result') {
         steps {
            sh '''
            kubectl apply -f ingress.yml || exit 1
            kubectl get ingress || exit 1
            '''
         }
      }
      stage('Validating Deployment') {
         steps {
            sh '''
            kubectl get pods,deployment,svc,ing || exit 1
            kubectl delete deployment,svc,ing --all || exit 1
            '''
         }
      }
   }
