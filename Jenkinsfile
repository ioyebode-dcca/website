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
                parallel 'Verify Git': {
                    stage('Verify Git') {
                      sh 'git --version'
                    }

                }, 'Verify Node': {
                    stage('Verify Node') {
                      sh 'npm -v'
                    }

                }, 'Verify AWS': {
                    stage('Verify AWS') {
                      sh 'aws -version'
                    }

                }, 'Verify Docker': {
                    stage('Verify Docker') {
                        sh 'docker -v'
                    }

                }, 'Verify terraform': {
                    stage('Verify Jq') {
                        sh 'terraform -v'
                    }
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
	stage ('check plan') {
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
	stage ('Destroy') {
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
