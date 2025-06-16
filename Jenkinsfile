pipeline {
    agent any

    environment {
        AZURE_SUBSCRIPTION_ID = 'f7819382-1199-4106-937c-e780748fd273'
        AZURE_TENANT_ID = 'ec1bd924-0a6a-4aa9-aa89-c980316c0449'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Debug - List Files') {
            steps {
                sh 'ls -l'
                sh 'ls -l terraform'
            }
        }

        stage('Azure CLI Login') {
            steps {
                sh '''
                    az login --identity || az login
                    az account set --subscription $AZURE_SUBSCRIPTION_ID
                '''
            }
        }

        stage('Packer Build') {
            steps {
                sh '''
                    packer init .
                    packer build ubuntu.pkr.hcl
                '''
            }
        }

        stage('Terraform Init & Plan') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                    sh 'terraform plan -out=tfplan'
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform apply -auto-approve tfplan'
                }
            }
        }
    }

    post {
        success {
            echo 'Packer build and Terraform deployment succeeded!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
