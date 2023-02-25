pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout') { 
            steps { 
                checkout scm
            }
        }
        stage('Set Terraform path') {
            steps {
                script {
                    env.PATH += ":/usr/bin/terraform"
                }
                sh 'terraform --version'
                sh 'pwd'
            }
        }
        stage('Verify Tools') {
            steps {
                parallel(
                    "Terraform": { sh 'terraform -v' },
                    "Docker": { sh 'docker -v' },
                    "AWS": { sh 'aws --version' }
                )
            }
        }
        stage('Verify Other Tools') {
            steps {
                parallel(
                    "Git": { sh 'git --version' },
                    "NPM": { sh 'npm -v' },
                    "Ansible": { sh 'ansible --version' }
                )
            }
        }
        stage('Terraform Plan') {
            steps {
                sh 'pwd'
                sh 'terraform init'
                sh 'terraform plan -input=false -out tfplan'
                sh 'terraform show -no-color tfplan > tfplan.txt'
            }
        }
        stage('check plan') {
            steps {
                input message: 'Is terraform plan okay?', ok: 'yes'
            }
        }
        stage ('Apply') {
            steps {
                input message: 'Do you want to Apply?', ok: 'yes'
                sh "terraform apply -input=false tfplan"
            }
        }
        stage('Build') { 
            steps { 
                script{
                    app = docker.build("underwater")
                }
            }
        }
        stage('Test'){
            steps {
                 echo 'Empty'
            }
        }
        stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://150685619118.dkr.ecr.us-east-1.amazonaws.com/', 'ecr:us-east-1:aws-credentials') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy'){
            steps {
                 sh 'kubectl get nodes'
                 sh 'kubectl apply -f deployment.yml'
                 sh 'kubectl rollout restart deployment ecr-app-underwater'
            }
        }    
    }
}
