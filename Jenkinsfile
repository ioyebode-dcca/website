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
                    env.PATH += ":/usr/local/bin/"
                }
                sh 'terraform --version'
		sh 'pwd'
            }
        }
	stage('version') {
            steps {
	      input ('Do you want to proceed?')	    
                sh """
                git --version
                uptime
                env
                ls -ltr
                """
            }
        }    
        stage('Terraform Init') {
            steps {
               sh 'pwd'
               sh 'terraform init -reconfigure'
               #sh 'terraform plan'
	       sh 'terraform show -no-color tfplan > tfplan.txt'
            }
        }
	stage ('check plan') {
            steps {
              input('Is terraform plan okay?')
            }
        } 
	stage ('Apply') {
            steps {
              input('Is terraform plan okay?')
	        sh "terraform apply -input=false tfplan"
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
