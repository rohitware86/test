def GetPOMData(PomName, PropertyName)
{
    try{
    def resultVal = ""
    def pomFile = readFile(PomName)
    def pom = new XmlParser().parseText(pomFile)
    def gavMap = [:]
    resultVal =  pom[PropertyName].text().trim()
    return resultVal
    }catch(error)
    {       
        //SendEmail("Failed")
        throw(error)
    }
}

pipeline {
    agent any
    environment{
        nexus_Protocol = "${protocol}"
    }
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
        stage('Upload to nexus') {
            steps {    
                nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: nexus_Protocol,
                            nexusUrl: '127.0.0.1:8081',
                            //groupId: 'com.mycompany',
                            groupId: GetPOMData("/Users/rohitware/Studio7/Contents/MacOS/it/test/pom.xml", "groupId")
                            version: '1.0.1-SNAPSHOT',
                            repository: 'Mule_Hosted',
                            credentialsId: 'nexus',
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: 'test',
                                //artifactId: readMavenPom().getArtifactId(),
                                classifier: '',
                                file: '/Users/rohitware/Studio7/Contents/MacOS/it/test/target/test-1.0.1-SNAPSHOT-mule-application.jar',
                                type: 'jar']
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                //[artifactId: pom.artifactId,
                                //classifier: '',
                                //file: "pom.xml",
                                //type: "pom"]
                            ]
                        )
            }
        }
        stage('Deploy CloudHub') { 
            environment {
                ANYPOINT_CREDENTIALS = credentials('ANYPOINT')
            }
            steps {
                sh 'mvn deploy -DmuleDeploy -Dmule.version=4.2.1 -Danypoint.username=${ANYPOINT_CREDENTIALS_USR} -Danypoint.password=${ANYPOINT_CREDENTIALS_PSW} -Dcloudhub.env=Sandbox -Danypoint.businessGroup=Mr'
            }
        }
    }
}