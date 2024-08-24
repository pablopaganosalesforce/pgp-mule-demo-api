pipeline {

  agent any
    tools {
      maven '3.9.9'
    }
    stages {
      stage('Build Application') {
        steps {
          script {
	              sh "mvn clean install"
	      }      	
        }
      }    
      stage('Publish to Exchange ') {
        when { branch 'develop' }
        steps {
          script {
            configFileProvider(
                [configFile(fileId: "mule_ppaganosalesforcemia_maven_settings", variable: 'MAVEN_SETTINGS')]) {
	            withCredentials([usernamePassword(credentialsId: 'mule_ppaganosalesforcemia_connected_app_credentials',
	            usernameVariable: 'USERNAME',
	            passwordVariable: 'PASSWORD')]) {
	              sh 'mvn deploy -settings $MAVEN_SETTINGS -Dclient_id=$USERNAME -Dclient_secret=$PASSWORD'
	            }
	         }
          }
        }
      }    
    }
}