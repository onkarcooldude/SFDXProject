//#!groovy
//@Library('sfdx-core-ci-lib') _
//import SfdxProjectBuilder
//new SfdxProjectBuilder(this)
//		.setScratchOrgDefFile('config/project-scratch-def.json')
//		.execute()
//import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH
    def toolbelt = tool 'toolbelt'

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    println toolbelt
    //def toolbelt = env.Toolbelt_D
    
    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }
     stage('logout') {
        // when running in multi-branch job, one must issue this command
	     rc = bat returnStatus: true, script: "echo y | \"${toolbelt}\" force:auth:logout --targetusername onkarcooldude@gmail.com"
    }
    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
				echo "Current workspace is $WORKSPACE"
				rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
			   //rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
				//rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:push --json --targetusername ${HUB_ORG}"
				//rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:org:create -s -f config/project-scratch-def.json --targetusername ${HUB_ORG}"
				//rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy -p SFDXProject/force-app -u ${HUB_ORG}"
				//rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy -p SFDXProject/manifest -u ${HUB_ORG}"
				//rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy -p SFDXProject/force-app -u ${HUB_ORG}"
				
				
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
