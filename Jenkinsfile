import groovy.json.JsonSlurper
import groovy.json.JsonOutput

//Global default variables
	String REGEX_ARTIFACT_ID = /bradfl-([a-z,-]*)-([e,s,p]{0,1}api|app)-(v[0-9]{1,})-impl/
    String MULE_APP_CLASSIFIER='mule-application'
	String MAVEN_SERVER_ID= 'anypoint-exchange-v3'
	String MULE_VERSION= '4.4.0'
	String RELEASE_CHANNEL='NONE'
	String JAVA_VERSION= '8'
	String DEPLOYMENT_TIMEOUT= '1000000'
	String SKIP_DEPLOYMENT_VERIFICATION = 'false'
	String UPDATE_STRATEGY = 'rolling'
	String FORWARD_SSL_SESSION = 'false'
	String LAST_MILE_SECURITY = 'false'
	String ENCRYPTION_KEY = ''
	String ENABLE_MONITORING = 'true'
	String API_MANAGER_STATUS = 'flexible'
    String PARENT_VERSION_MIN='1.0.4'

//Api related variables
	String OBJECT_STORE_V2 = 'false'
	String VCORES='0.1'
	String REPLICAS='1'

//Environment and pom.xml generated variables
	String VISUALIZER_LAYER = ''
	String PRIVATE_SPACE = ''
	String BUSINESS_GROUP_ID='e2db758a-a779-46e3-a4f1-e3e8522a390d'
	String ENVIRONMENT= ''
	String ARTIFACT_ID=''
    String APPLICATION_NAME=''
	String TAG_BRANCH=""
	String TAG_ENVIRONMENT=""
	String PRIVATE_SPACE_NON_PROD='PPAGANO-NON-PROD'
	String PRIVATE_SPACE_PROD='PPAGANO-PROD'
    String ENV_APPLICATION_NAME_DEV="dev"
    String ENV_APPLICATION_NAME_UAT="uat"
    String ENV_APPLICATION_NAME_PROD="prod"
    String ENVIRONMENT_DEV="DEV"
    String ENVIRONMENT_UAT="UAT"
    String ENVIRONMENT_PROD="PROD"
    String TAG_ENVIRONMENT_DEV="-DEV"
    String TAG_ENVIRONMENT_UAT="-UAT"
    String TAG_ENVIRONMENT_PROD=""
    String TAG_BRANCH_DEVELOP='develop'
    String TAG_BRANCH_STAGING='staging'
    String TAG_BRANCH_MAIN='main'
    String PARENT_ARTIFACT_ID=''
    String PARENT_VERSION=''
    String DEPLOYMENT_LINK
    def access_token
    def updatedJsonContent
    def gitBranch
    //def sonarProjectName
    def GIT_URL_ENV
//Variables de Jenkins
    String LINK_BUILD_URL=''

//Archivos de configuracion en Jenkins
    String mule_properties_file
    String mule_dev_properties_file
    String mule_uat_properties_file
    String mule_prod_properties_file
    String config_properties_file
    String local_env_properties_file
//Connection to Jenkins configuration files for variables
/*
node(){
            configFileProvider(
                [configFile(fileId: "mule.properties", variable: 'mule_properties')]) {
                	   mule_properties_file = readFile "$mule_properties"
            }
            configFileProvider(
                [configFile(fileId: "mule_dev.properties", variable: 'mule_dev_properties')]) {
                	   mule_dev_properties_file = readFile "$mule_dev_properties"
            }
            configFileProvider(
                [configFile(fileId: "mule_uat.properties", variable: 'mule_uat_properties')]) {
                	  mule_uat_properties_file = readFile "$mule_uat_properties"
            }
            configFileProvider(
                [configFile(fileId: "mule_prod.properties", variable: 'mule_prod_properties')]) {
                	   mule_prod_properties_file = readFile "$mule_prod_properties"
            }
            configFileProvider(
                [configFile(fileId: "pipeline-multibranch-bradfl-poc-cicd-sapi-v1-impl_config.properties", variable: 'config_properties')]) {
                       config_properties_file = readFile "$config_properties"
                       //echo config_properties_file
            }

}
*/
pipeline {

  agent any
    tools {
      maven '3.9.9'
    }
    environment {
      IMAGE = readMavenPom().getArtifactId()
      VERSION = readMavenPom().getVersion()
    }
    stages {
      stage('Set global variables and check naming conventions') {
        steps {
          script {
                if (env.BRANCH_NAME.contains('develop')) {
	               ENV_APPLICATION_NAME=ENV_APPLICATION_NAME_DEV
	               ENVIRONMENT=ENVIRONMENT_DEV
	               TAG_ENVIRONMENT=TAG_ENVIRONMENT_DEV
	               TAG_BRANCH=TAG_BRANCH_DEVELOP
	               PRIVATE_SPACE=PRIVATE_SPACE_NON_PROD
	            }
	            if (env.BRANCH_NAME.contains('staging')) {
	               ENV_APPLICATION_NAME=ENV_APPLICATION_NAME_UAT
	               ENVIRONMENT=ENVIRONMENT_UAT
	               TAG_ENVIRONMENT=TAG_ENVIRONMENT_UAT
	               PRIVATE_SPACE=PRIVATE_SPACE_NON_PROD
	               TAG_BRANCH=TAG_BRANCH_STAGING
	            }
	            if (env.BRANCH_NAME.contains('main')) {
	               ENV_APPLICATION_NAME=ENV_APPLICATION_NAME_PROD
	               TAG_ENVIRONMENT=TAG_ENVIRONMENT_PROD
	               ENVIRONMENT=ENVIRONMENT_PROD
	               PRIVATE_SPACE=PRIVATE_SPACE_PROD
	               TAG_BRANCH=TAG_BRANCH_MAIN
	            }
	            //Set pom.xml variables
                def file = readFile "pom.xml"

	            def pomXml = new XmlSlurper().parseText(file)
	            /*
	            PARENT_VERSION = pomXml.parent.version.toString()
	            PARENT_ARTIFACT_ID = pomXml.parent.artifactId.toString()
	            */
	            //Set variables from pom.xml
                VERSION = pomXml.version.toString().replace("-SNAPSHOT","")
                APPLICATION_NAME = pomXml.name.toString()
                BUSINESS_GROUP_ID = pomXml.groupId.toString()
                ARTIFACT_ID = pomXml.artifactId.toString()
                pomXml=null
	            if (ARTIFACT_ID.contains('sapi')) {
	              VISUALIZER_LAYER="System"
	            }
	            if (ARTIFACT_ID.contains('papi')) {
	              VISUALIZER_LAYER="Process"
	            }
	            if (ARTIFACT_ID.contains('eapi')) {
	              VISUALIZER_LAYER="Experience"
	            }
	              VISUALIZER_LAYER="System"
                String pathLocalEnvFile= "pipeline/" + ENV_APPLICATION_NAME + ".properties"
                local_env_properties_file = readFile pathLocalEnvFile
	            //Set global variables
	            REGEX_ARTIFACT_ID = getProperty("$ENV_APPLICATION_NAME", "REGEX_ARTIFACT_ID", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","").replace("/","")
	            MAVEN_SERVER_ID = getProperty("$ENV_APPLICATION_NAME", "MAVEN_SERVER_ID", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            MULE_VERSION = getProperty("$ENV_APPLICATION_NAME", "MULE_VERSION", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            RELEASE_CHANNEL = getProperty("$ENV_APPLICATION_NAME", "RELEASE_CHANNEL", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            JAVA_VERSION = getProperty("$ENV_APPLICATION_NAME", "JAVA_VERSION", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            DEPLOYMENT_TIMEOUT = getProperty("$ENV_APPLICATION_NAME", "DEPLOYMENT_TIMEOUT", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            SKIP_DEPLOYMENT_VERIFICATION = getProperty("$ENV_APPLICATION_NAME", "SKIP_DEPLOYMENT_VERIFICATION", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            UPDATE_STRATEGY = getProperty("$ENV_APPLICATION_NAME", "UPDATE_STRATEGY", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            FORWARD_SSL_SESSION = getProperty("$ENV_APPLICATION_NAME", "FORWARD_SSL_SESSION", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            LAST_MILE_SECURITY = getProperty("$ENV_APPLICATION_NAME", "LAST_MILE_SECURITY", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            ENCRYPTION_KEY = getProperty("$ENV_APPLICATION_NAME", "ENCRYPTION_KEY", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            ENABLE_MONITORING = getProperty("$ENV_APPLICATION_NAME", "ENABLE_MONITORING", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            API_MANAGER_STATUS = getProperty("$ENV_APPLICATION_NAME", "API_MANAGER_STATUS", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            MULE_APP_CLASSIFIER = getProperty("$ENV_APPLICATION_NAME", "MULE_APP_CLASSIFIER", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            PARENT_VERSION_MIN = getProperty("$ENV_APPLICATION_NAME", "PARENT_VERSION_MIN", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")

                //Set api env variables
	            VCORES = getProperty("$ENV_APPLICATION_NAME", "VCORES", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            REPLICAS = getProperty("$ENV_APPLICATION_NAME", "REPLICAS", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
	            OBJECT_STORE_V2 = getProperty("$ENV_APPLICATION_NAME", "OBJECT_STORE_V2", mule_properties_file, mule_dev_properties_file, mule_uat_properties_file, mule_prod_properties_file).replace("'","")
                /*
                //Check parent pom and naming conventions
	            assert compareVersions(PARENT_VERSION_MIN, "$PARENT_VERSION") == true
	            assert PARENT_ARTIFACT_ID == "bradfl-parent-pom"
	            assert APPLICATION_NAME == ARTIFACT_ID
	            assert APPLICATION_NAME =~ REGEX_ARTIFACT_ID
                assert APPLICATION_NAME.size() <=42
                */
                //Generate Git url with branch
                //GIT_URL_ENV = env.GIT_URL
                //GIT_URL_ENV = GIT_URL_ENV.replaceAll('.git$', '') + "/-/tree/" + env.BRANCH_NAME
                //echo "GIT_URL_ENV: $GIT_URL_ENV"

                //Replace name artifact id
                //APPLICATION_NAME = APPLICATION_NAME.replace("impl","$ENV_APPLICATION_NAME")
                //sonarProjectName = APPLICATION_NAME + "-" + VERSION
                //Replace build url for email
                //LINK_BUILD_URL =  env.BUILD_URL
                //LINK_BUILD_URL = LINK_BUILD_URL.replace("http://jenkins:8080","https://jenkins.devops.k8s.bradescobank.com")
                //test pipeline-multibranch-bradfl-poc-cicd-sapi-v1-impl_config.properties
                //String PROPIEDAD1 = getDefaultProperty("$ENV_APPLICATION_NAME","PROPIEDAD1",config_properties_file).replace("'","")
                //echo "PROPIEDAD1: $PROPIEDAD1"

          }
        }
      }
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
      stage('Deploy to CloudHub DEV') {
        when { branch 'develop' }
        steps {
          script {
            configFileProvider(
                [configFile(fileId: "mule_ppaganosalesforcemia_maven_settings", variable: 'MAVEN_SETTINGS')]) {
	            withCredentials([usernamePassword(credentialsId: 'mule_ppaganosalesforcemia_connected_app_credentials',
	            usernameVariable: 'USERNAME',
	            passwordVariable: 'PASSWORD'),
	            usernamePassword(credentialsId: 'mule_ppaganosalesforcemia_dev_credentials',
	            usernameVariable: 'ANYPOINT_CLIENT_ID',
	            passwordVariable: 'ANYPOINT_CLIENT_SECRET')]) {
                try {
	              sh "mvn deploy -settings $MAVEN_SETTINGS -Dclient_id=$USERNAME -Dclient_secret=$PASSWORD \
	              -DmuleDeploy -Dcloudhub2.target=$PRIVATE_SPACE \
	              -Dcloudhub2.mavenServerId=$MAVEN_SERVER_ID -Dcloudhub2.applicationName=$APPLICATION_NAME \
				  -Dproject.artifactId=$IMAGE -Dproject.version=$VERSION -Dmule.app.classifier=$MULE_APP_CLASSIFIER \
				  -Dcloudhub2.businessGroupId=$BUSINESS_GROUP_ID -Dcloudhub2.environment=$ENVIRONMENT \
				  -Dcloudhub2.muleVersion=$MULE_VERSION -Dcloudhub2.releaseChannel=$RELEASE_CHANNEL \
				  -Dcloudhub2.javaVersion=$JAVA_VERSION -Dcloudhub2.replicas=$REPLICAS \
				  -Dcloudhub2.vCores=$VCORES -Dcloudhub2.deploymentTimeout=$DEPLOYMENT_TIMEOUT \
				  -Dcloudhub2.skipDeploymentVerification=$SKIP_DEPLOYMENT_VERIFICATION -Dcloudhub2.updateStrategy=$UPDATE_STRATEGY -Dcloudhub2.forwardSslSession=$FORWARD_SSL_SESSION \
				  -Dcloudhub2.lastMileSecurity=$LAST_MILE_SECURITY -Dcloudhub2.objectStoreV2=$OBJECT_STORE_V2 \
                  -Dmule.encryptionKey=$ENCRYPTION_KEY -Dmule.environment=$ENVIRONMENT -Danypoint.platform.clientId=$ANYPOINT_CLIENT_ID \
				  -Danypoint.platform.clientSecret=$ANYPOINT_CLIENT_SECRET -Danypoint.platform.enableMonitoring=$ENABLE_MONITORING \
				  -Danypoint.platform.visualizerLayer=$VISUALIZER_LAYER -Danypoint.platform.apiManagerStatus=$API_MANAGER_STATUS "
                } catch (Exception e) {
                    echo 'Jenkins Exception occurred: ' + e.toString()
                      //echo 'Jenkins Exception occurred'
                }
	            }
	         }
          }
        }
      }
    }


}
String getValue(String param, LinkedHashMap config_pipeline, LinkedHashMap config_pipeline_global){

  if (config_pipeline.get(param) != null) {
    value = config_pipeline.get(param).toString()
  }
  else
  {
    if (config_pipeline_global.get(param) != null) {
      value = config_pipeline_global.get(param).toString()
    }
  }
  return value
}
Properties buildProperties(String propertiesFromString, String entrySeparator) throws IOException {
    Properties properties = new Properties();
    properties.load(new StringReader(propertiesFromString.replaceAll(entrySeparator, "\n")));
    return properties;
}

Boolean compareVersions(String version1, String version2) {
    Boolean result=true
    //echo version1
    List verA = version1.tokenize('.')
    List verB = version2.tokenize('.')
    if (verA.size()==3 && verB.size()==3) {
        for (int i = 0; i < verA.size(); ++i) {
          def numA = verA[i].toInteger()
          def numB = verB[i].toInteger()
          //echo numA.toString()
          //echo numB.toString()
          if (numA <= numB && result) {
          }
          else
          {
              result=false
          }
        }
    }
    else
    {
       result=false
    }
    return result
}
String getProperty(String env, String propName, String mule_properties_file, String mule_dev_properties_file, String mule_uat_properties_file,String mule_prod_properties_file){
//*
	if (!getAppEnvProperty(env, propName).equals(null)
	    &&!getAppEnvProperty(env, propName).equals("\"\"")
	    &&!getAppEnvProperty(env, propName).equals("\'\'")) {
		property = getAppEnvProperty(env, propName).toString().replace("'","")
		//echo property
	}
	else{
		if (!getAppProperty(propName).equals(null)
    	    &&!getAppProperty(propName).equals("\"\"")
    	    &&!getAppProperty(propName).equals("\'\'")) {
    	//if (Optional.ofNullable(getAppProperty(propName))){
			property = getAppProperty(propName)
		}
		else{
			if (!getDefaultEnvProperty(env,propName,mule_dev_properties_file, mule_uat_properties_file,mule_prod_properties_file).equals(null)
			    &&!getDefaultEnvProperty(env,propName,mule_dev_properties_file, mule_uat_properties_file,mule_prod_properties_file).equals("\"\"")
        	    &&!getDefaultEnvProperty(env,propName,mule_dev_properties_file, mule_uat_properties_file,mule_prod_properties_file).equals("\'\'")) {
			//if (getDefaultEnvProperty(env,propName,mule_dev_properties_file, mule_uat_properties_file,mule_prod_properties_file).equals(null)||!getDefaultEnvProperty(env,propName,mule_dev_properties_file, mule_uat_properties_file,mule_prod_properties_file).equals("")) {
			    property = getDefaultEnvProperty(env,propName,mule_dev_properties_file, mule_uat_properties_file,mule_prod_properties_file)
			    //property = getDefaultEnvProperty(env, propName, mule_dev_properties_file, mule_prod_properties_file)
			}
			else{
               if (!getDefaultProperty(env, propName, mule_properties_file).equals(null)
                    &&!getDefaultProperty(env, propName, mule_properties_file).equals("\"\"")
                    &&!getDefaultProperty(env, propName, mule_properties_file).equals("\'\'")) {
               //if (getDefaultProperty(env, propName, mule_properties_file).equals("")||getDefaultProperty(env, propName, mule_properties_file).equals(null)){
                 property = getDefaultProperty(env, propName, mule_properties_file)
               }
			}
		}
	}
	return property
}
String getAppEnvProperty(String env, String propName){
	String pathLocalEnvFile= "pipeline/" + env + ".properties"
	String local_env_properties_file = readFile pathLocalEnvFile
	Properties properties = buildProperties(local_env_properties_file,"\n")
	String property = properties.getProperty(propName)
	return property
}

String getAppProperty(String propName){
	String default_properties_file = readFile "pipeline/default.properties"
	Properties properties = buildProperties(default_properties_file,"\n")
	String property = properties.getProperty(propName)
	return property
}

String getDefaultEnvProperty(String env, String propName,String mule_dev_properties_file, String mule_uat_properties_file,String mule_prod_properties_file ){
//String getDefaultEnvProperty(String env, String propName,String mule_dev_properties_file,String mule_prod_properties_file ){
	switch (env) {
	case "dev":
	    mule_env_properties_file = mule_dev_properties_file
	    break
    case "uat":
        mule_env_properties_file = mule_uat_properties_file
        break
	case "prod":
	    mule_env_properties_file = mule_prod_properties_file
	    break
    default:
        mule_env_properties_file = null
        break;
	}
	//echo mule_env_properties_file
	Properties properties = buildProperties(mule_env_properties_file,"\n")
	String property = properties.getProperty(propName)
	return property
}
String getDefaultProperty(String env,String propName,String mule_properties_file){
	Properties properties = buildProperties(mule_properties_file,"\n")
	String property = properties.getProperty(propName)
	return property
}
S