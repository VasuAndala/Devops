#!groovy

import groovy.json.JsonSlurperClassic

node {

    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY
    def SF_USERNAME=env.SF_USERNAME
    def SERVER_KEY_CREDENTALS_ID=env.SERVER_KEY_CREDENTALS_ID
    def TEST_LEVEL='RunLocalTests'
    def PACKAGE_NAME='0Ho1U000000CaUzSAK'
    def PACKAGE_VERSION
    def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"

    def toolbelt = tool 'toolbelt'


    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------
    
        withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {

            // -------------------------------------------------------------------------
            // Authorize the Dev Hub org with JWT key and give it an alias.
            // -------------------------------------------------------------------------

            stage('Authorize Org') {
                rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instance-url ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwt-key-file ${server_key_file}"
                if (rc != 0) {
                    error 'Salesforce dev hub org authorization failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Deploy the Code to Targeted Org
            // -------------------------------------------------------------------------

            stage('Deploy') {
                rc = command "${toolbelt}/sfdx force:deploy:mdapi:deploy -d force-app/main/default -u ${SF_USERNAME} -w 1000 --testlevel NoTestRun"
                if (rc != 0) {
                    error 'Salesforce test scratch org creation failed.'
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
