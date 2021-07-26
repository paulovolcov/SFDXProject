#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
	def CONNECTED_APP_CONSUMER_KEY_TARGET = 'sfdx-devpv-consumerkey'
    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }
	
	//withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')])
	
	withCredentials([
                     usernamePassword(credentialsId: 'sfdx-devpv', usernameVariable: 'USERNAME_TARGET', passwordVariable: 'PASSWORD_TARGET'),
                     file(credentialsId: 'sfdc-server-key', variable: 'jwt_key_file'),
                     string(credentialsId: "${CONNECTED_APP_CONSUMER_KEY_TARGET}", variable: 'SECURE_KEY_TARGET')]){
        stage('Deploy Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${SECURE_KEY_TARGET} --username ${USERNAME_TARGET} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl https://test.salesforce.com"
			}else{
                rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${SECURE_KEY_TARGET} --username ${USERNAME_TARGET} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl https://test.salesforce.com"
            }
            if (rc != 0) { error 'hub org authorization failed' }
 
			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "\"${toolbelt}\" force:source:deploy -p force-app --checkonly -u ${USERNAME_TARGET}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy -p force-app --checkonly -u ${USERNAME_TARGET}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
