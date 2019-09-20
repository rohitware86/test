def GetPOMData(PomName, PropertyName)
{
    try{
    echo "Inside the GetPOMData"
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
        nexus_url = "${url}"
        nexus_repo = "${repo}"
        groupID = GetPOMData("/Users/rohitware/Studio7/Contents/MacOS/it/test/pom.xml", "groupId")
        version = GetPOMData("/Users/rohitware/Studio7/Contents/MacOS/it/test/pom.xml", "version")
        artifcatID = GetPOMData("/Users/rohitware/Studio7/Contents/MacOS/it/test/pom.xml", "artifactId")

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
                echo 'build done!!'
      		}
        }
        stage('Unit Testing') {
            steps {
                sh 'mvn test'
                echo 'testing done'
            }
        }
        stage('Upload to nexus') {
            steps {    
                nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: nexus_Protocol,
                            nexusUrl: nexus_url,
                            //groupId: 'com.mycompany',
                            groupId: groupID,
                            version: version,
                            repository: nexus_repo,
                            credentialsId: 'nexus',
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: artifcatID,
                                //artifactId: readMavenPom().getArtifactId(),
                                classifier: '',
                                //file: '/Users/rohitware/Studio7/Contents/MacOS/it/test/target/test-1.0.1-SNAPSHOT-mule-application.jar',
                                file: "/Users/Shared/Jenkins/Home/workspace/Test_Mule/target/${artifcatID}-${version}-mule-application.jar",
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