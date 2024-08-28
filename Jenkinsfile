pipeline {
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
                    echo "Updating kubeconfig..."
                    aws eks --region ${AWS_REGION} update-kubeconfig --name ${CLUSTER_NAME}
                    echo "Kubeconfig updated. Checking pods..."
                    kubectl get pods -A || exit 1
                    echo "Checking namespaces..."
                    kubectl get ns || exit 1
                    '''
                }
            }
        }
        stage('Deploy Voting App') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                sh '''
                echo "Deploying Voting App..."
                kubectl apply -f voting-with-ingress.yml || exit 1
                kubectl apply -f ingress.yml || exit 1
                kubectl apply -f ingress-nk.yml || exit 1
                kubectl get pods,svc,ingress || exit 1
                '''
            }
        }
        stage('Deploy Ingress for Vote & Result') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                sh '''
                echo "Deploying Ingress..."
                kubectl apply -f ingress.yml || exit 1
                kubectl get ingress || exit 1
                '''
            }
        }
        stage('Validating Deployment') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                sh '''
                echo "Validating Deployment..."
                kubectl get pods,deployment,svc,ing || exit 1
                echo "Cleaning up resources..."
                kubectl delete deployment,svc,ing --all || exit 1
                '''
            }
        }
    }
}
