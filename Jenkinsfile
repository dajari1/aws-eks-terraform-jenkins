pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION = 'us-east-1'
    }
    parameters {
        
        
        choice choices: ['apply', 'destroy'], description: '''Choose your terraform action
        ''', name: 'action'
    }
    stages{
        stage('Checkout SCM'){
            steps{
                script{
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/dajari1/aws-eks-terraform-jenkins.git']])
                }
            }
        }
        stage('Initializing Terraform'){
            steps{
                script{
                    dir('terraform'){
                         sh 'terraform init'
                    }
                }
            }
        }
        stage('Validating Terraform'){
            steps{
                script{
                    dir('terraform'){
                         sh 'terraform validate'
                    }
                }
            }
        }
        stage('Previewing the infrastructure'){
            steps{
                script{
                    dir('terraform'){
                         sh 'terraform plan'
                    }
                    input(message: "Approve?", ok: "proceed")
                }
            }
        }
        stage('Create/Destroy an EKS cluster'){
            steps{
                script{
                    dir('terraform'){
                         sh 'terraform $action --auto-approve'
                    }
                }
            }
        }
        stage('Deploying to Kubernetes') {
          steps {
            script {
            dir('manifests') {
              sh ('aws eks update-kubeconfig --name aws-eks-cluster --region us-east-1')
              sh "kubectl get ns"
              sh "kubectl apply -f deployment.yaml"
              sh "kubectl apply -f service.yaml"
          }
        }
      }
    }

  }
}
    
