pipeline {
    agent any
    tools {
		maven 'mule_maven'
	}
    stages {
        stage('Stage 1') {
            steps {
                echo 'Hello world!' 
            }
        }
        stage('Clean') { 
			steps {
				sh 'mvn clean'
			}
		}
    	stage('Build and Package') { 
     		 steps {
        		sh 'mvn package'
      		}
        }
        stage('Unit Testing') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy CloudHub') { 
            environment {
                ANYPOINT_CREDENTIALS = credentials('ANYPOINT')
            }
            steps {
                sh 'mvn deploy -P cloudhub -Dmule.version=4.2.1 -Danypoint.username=${ANYPOINT_CREDENTIALS_USR} -Danypoint.password=${ANYPOINT_CREDENTIALS_PSW} -Danypoint.businessGroup=Mr' 
            }
        }
    }
}