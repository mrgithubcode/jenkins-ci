#!groovy
node('linux && ansible') {

	try{
		def jdkHome
		def mvnHome
		def artifactVersion
		def myprojectEnv
		def myprojectSystemName
		def myprojectProjectName	
		def targetPackageName
		def deploymentPackageName
		def genericReleaseDir
		def myprojectDir
		def config
		def configFileName 
		def depolymentPackage
		
		stage('Source') {
			deleteDir()
			checkout scm
			notifyStash
		}
		
		stage('Set up environment') {
			//building the deployment setup
			myprojectSystemName = "MYSYSTEMNAME"		
			targetPackageName = "DEPLOY_COMMON_v1.zip"
						  
			myprojectProjectName = "Deployment"
			myprojectEnv = "dev"
			deploymentPackageName = "DEPLOY_COMMON.zip"		
			
			genericReleaseDir = "generic-release_local"
			myprojectDir = "generic-myproject"
			
			configFileName = "Deployment"
			
			if (fileExists("./${configFileName}.yml")) {
				config = readYaml file: "./${configFileName}.yml"
				echo "config ==> ${config}"
			}
			else {
				config = []
			}
			
			PrintInfo('config file succesfully loaded')
			
			//creating artifacts folder inside workspace to hold artifacts(jar, nars)
			sh 'mkdir artifacts'
		}
	 
		stage("Downloading artifacts") {			
			PrintInfo('staring artifacts download')				
			if(config && config.deployment && config.deployment.spark_batch_deployment)			{
				PrintInfo('spark batch deployment is enabled')
				ArtifactDownload(config.artifactory.artifactoryUrl, config.spark.artifactory_repo, config.spark.group_id, config.spark.spark_artifactId, config.deployment.spark_batch_jar_version,config.spark.file_type, "$WORKSPACE/artifacts")	
			}
			else{
				PrintInfo('spark batch deployment is disabled')
			}					
					
		}
		stage("creating Deployment package"){

			echo "staring packaging process" 
			depolymentPackage = config.deployment.deployment_Package
			zip = sh(returnStdout: true, mycompanyript: "zip ${depolymentPackage}.zip artifacts/* DEPLOY_COMMON/* ")
			echo "Packaging in progress : ${zip}"
		
			sh '''		
			cksum=`sha1sum ${ansiblePackageName} | awk -F" " '{print $1}'`		
			echo "Checksum for playbook -- $cksum"
			'''			
		}
		
		stage('Uploading deployment package to artifactory'){
			PrintInfo('Staring uploading artifacts to Artifactory')
			
			deploymentPackageName = "${depolymentPackage}.zip"
			echo "deploymentPackageName - ${deploymentPackageName}"
			
			uploadArtifactToGenericTemp { 			    
				source = "${deploymentPackageName}"
				target = "${deploymentPackageName}"
			}
			
			moveArtifactFromGenericTemp { 
				source="${deploymentPackageName}"
				target=config.artifactory.generic_repo + "/" + config.artifactory.myproject_dir + "/" + config.artifactory.deployment_dir + "/" + "${deploymentPackageName}"
			} 
		}
		
		stage('success')	{
			//sender, recipient, subject, mailBody, buildUrl 	
		SendMail( 'Jenkinsbuild@JenkinsMail.com', 'email.S@mycompany.com', 'email@mycompany.com', 'Jenkins Build status - success' , 'Build is succesfully completed and you could see the details here', "${env.BUILD_URL}")				
		}
		
	}
	catch (err) {

		currentBuild.result = "FAILURE"
		
		//sender, recipient-to, recipient-cc, subject, mailBody, buildUrl 	
		SendMail( 'Jenkinsbuild@JenkinsMail.com', 'email@mycompany.com', 'email2@mycompany.com', 'Jenkins Build status - failure' , 'Build has failed and you could see the details here', "${env.BUILD_URL}")				

		throw err
	}

}

def PrintInfo(String printMessage) { 			
		echo "${printMessage}"
	}
		
def ArtifactDownload(String artifactoryUrl, String mavenRepo, String groupId, String artifacId, String version, String fileType, String destDir) {		
		def artifactUrl = "${artifactoryUrl}/${mavenRepo}/${groupId}/${artifacId}/${version}/${artifacId}-${version}.${fileType}"
	 	echo "${artifactUrl}"
		download = sh(returnStdout: true, mycompanyript: "wget --auth-no-challenge -P ${destDir} ${artifactUrl}")				
}

def SendMail(String sender, String toRecipient, String ccRecipient , String subject, String mailBody, String buildUrl){
		mail body: "${mailBody} : ${buildUrl}" ,
		from: "${sender}",		
		subject: "${subject}",
		to: "${toRecipient}",
		cc: "${ccRecipient}"
}

		

