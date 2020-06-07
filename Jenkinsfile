#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG
    def SFDC_HOST = "https://login.salesforce.com"
    def JWT_KEY_CRED_ID = "04af10e1-e5f7-46d2-bf3f-7b051b08d37a"
    def CONNECTED_APP_CONSUMER_KEY

    def DEVELOPER_HUB_ORG="dev@softechmedia.com"
    def PRODUCTION_HUB_ORG="production@softechmedia.com"
    def UAT_HUB_ORG="uat@softechmedia.com"

    def DEVELOPER_CONSUMER_KEY="3MVG97quAmFZJfVxLVIrarqTf60vzrfrFpKyffK.cB9CL30cOIyD9Tp.oaejCic5R3mu.ru7.8g=="
    def PRODUCTION_CONSUMER_KEY="3MVG97quAmFZJfVz6cc3ZtNrDoeF4sSsdx1gZl0s9JCdwCMl4v2DFwOVivYJvVh4up5DnujsgLlitAKX5e9ro"
    def UAT_CONSUMER_KEY="3MVG97quAmFZJfVwLLlI2u1B0Sv.QhsvqIcFtDUZgrt1qXk0.JpHW.yaxFnlvwdAuCmDP5ihAqoM_hBg5_FCi"
    def branchname
    
    branchname=scm.branches[0].name

    if(branchname.contains('develop')) { 
        HUB_ORG=DEVELOPER_HUB_ORG
        CONNECTED_APP_CONSUMER_KEY=DEVELOPER_CONSUMER_KEY
    }
    else if(branchname.contains('UAT')) { 
        HUB_ORG=UAT_HUB_ORG
        CONNECTED_APP_CONSUMER_KEY=UAT_CONSUMER_KEY
    }
    else if(branchname.contains('Production')) { 
        HUB_ORG=PRODUCTION_HUB_ORG
        CONNECTED_APP_CONSUMER_KEY=PRODUCTION_CONSUMER_KEY
    }
    println 'Branch is'
    println branchname 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm

    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {

        if(CONNECTED_APP_CONSUMER_KEY){
 stage('Autherising Org') {
                if (isUnix()) {
                    rc = sh returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else {
                    rc = bat returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                }
                if (rc != 0) { error 'hub org authorization failed' }

                println rc
            }
            stage('Creating deploy directory') {
                if (isUnix()) {
                    rmsg = sh returnStdout: true, script: 'sfdx force:source:convert -r force-app/main/default  -d deploy'
            }else {
                    rmsg = bat returnStdout: true, script: 'sfdx force:source:convert -r force-app/main/default  -d deploy'
                }
            }
            stage('Deploying Code') {
                // need to pull out assigned username
                if (isUnix()) {
                    rmsg = sh returnStdout: true, script: "sfdx force:mdapi:deploy -w 3 -d deploy. -u ${HUB_ORG}"
            }else {
                    rmsg = bat returnStdout: true, script: "sfdx force:mdapi:deploy -w 3 -d deploy -u ${HUB_ORG}"
                }

                printf rmsg
                println('Hello from a Job DSL script!')
                println(rmsg)
            }       }
    }
}