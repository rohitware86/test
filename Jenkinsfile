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
    }
}