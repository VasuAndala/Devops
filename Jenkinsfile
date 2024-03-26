#!groovy

import groovy.json.JsonSlurperClassic

node {

    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY
    def SF_USERNAME=env.SF_USERNAME
    def SERVER_KEY_CREDENTALS_ID=env.SERVER_KEY_CREDENTALS_ID

    def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"

    	println SF_USERNAME
	println SF_CONSUMER_KEY
	println 'Check Server Key data'
	println SERVER_KEY_CREDENTALS_ID
	println SF_INSTANCE_URL
	
	def toolbelt = tool 'toolbelt'


    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

	
    stage('checkout source') {

		    cleanWs()
        checkout scm
	    println 'Checkout done'
	    
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------
    
        withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {

		println 'withCredentials'
            // -------------------------------------------------------------------------
            // Authorize the Dev Hub org with JWT key and give it an alias.
            // -------------------------------------------------------------------------

            stage('Authorize Org') {
		    println 'auth org'
                rc = bat returnStatus: true,script: "${toolbelt} sfdx force:auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${server_key_file}"
                if (rc != 0) {
                    error 'Salesforce dev hub org authorization failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Deploy the Code to Targeted Org
            // -------------------------------------------------------------------------

            stage('Deploy') {
                rc = bat returnStdout: true, script: "${toolbelt} sfdx force:mdapi:deploy -d force-app/main/default/ -u ${SF_USERNAME} -w 1000 --testlevel NoTestRun"
                if (rc != 0) {
                    error 'Salesforce deployment failed.'
                }
            }            
		
          
        }
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
        return bat(returnStatus: true, script: script);
    }
}
