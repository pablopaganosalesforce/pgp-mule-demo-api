pipeline {

  agent any
    tools {
      maven '3.9.9'
    }
    stages {
      stage('Build Application') {
        steps {
          script {
            configFileProvider(
                [configFile(fileId: "mule_maven_cicd_settings.xml", variable: 'MAVEN_SETTINGS')]) {

	            withCredentials([usernamePassword(credentialsId: 'mule_connectedApp_jenkinsDevOps_credentials',
	            usernameVariable: 'USERNAME',
	            passwordVariable: 'PASSWORD')]) {
	              sh "mvn clean install"
	            }
            }
          }
        }
      }    
		}
	}
}