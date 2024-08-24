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
    }
}