// Jenkinsfile
String credentialsId = 'awsCredentials'
pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    stages {
        stage('checkout') {
            steps {
                cleanWs()
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
                    "step 1": { sh 'terraform -v' },
                    "step 2": { sh 'docker -v' },
                    "step 3": { sh 'aws --version' }
                )
            }
        }
        stage('Verify Other Tools') {
            steps {
                parallel(
                    "step 1": { sh 'git --version' },
                    "step 2": { sh 'npm -v' },
                    "step 3": { echo "world" }
                )
            }
        }
        stage('Terraform Init') {
            steps {
                sh 'pwd'
                sh 'terraform init'
                sh 'terraform plan -input=false -out tfplan'
                sh 'terraform show -no-color tfplan > tfplan.txt'
            }
        }
        stage('check plan') {
            steps {
                input('Is terraform plan okay?')
            }
        }
        //stage ('Apply') {
        //  steps {
        //  input('Do you want to Apply?')
        //  sh "terraform apply -input=false tfplan"
        //  }
        //}
        stage('Destroy') {
            steps {
                input('Do you want to DESTROY?')
                sh "terraform destroy --auto-approve"
            }
        }
        stage('Ansible version') {
            steps {
                sh """
                ansible --version
                """
            }
        }
    }
}
