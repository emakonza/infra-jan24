pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('evi_AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('evi_AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION = "eu-west-2"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/emakonza/infra-jan24.git'
            }
        }

        stage("terraform init") {
            steps {
                sh "terraform init -migrate-state" 
            }
        }

        stage("plan") {
            steps {
                sh "terraform plan" 
            }
        }

        stage("Action") {
            steps {
                // Make sure `action` is defined somewhere
                sh "terraform ${action} --auto-approve" 
            }
        }

        stage("Deploy to EKS") {
            when {
                expression { params.apply }
            }
            steps {
                sh "aws eks update-kubeconfig --name eks_cluster"
                sh "kubectl apply -f deployment.yml"
            }
        }
    }
}
