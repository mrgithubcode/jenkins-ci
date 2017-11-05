#!groovy
node('linux && ansible') {
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
    
    stage('Source') {
        checkout scm
        notifybitbucket
    }
    
    stage('Build environment') {
        
		myprojectSystemName = "SYSTEMNAME"		
		targetPackageName = "DEPLOY_COMMON_v1.zip"
		
		jdkHome = installTool "jdk1.8.0_91"
        	mvnHome = installTool "apache-maven-3.3.9"        
		myprojectProjectName = "Deployment"
		myprojectEnv = "dev"
		deploymentPackageName = "DEPLOY_COMMON.zip"		
		
		genericReleaseDir = "generic-release_local"
		myprojectDir = "generic-myproject"
		
    }
 
    stage("Packaging and Artifact Publish") {	

		
		echo "staring deployment packaging process" 
		
		zip = sh(returnStdout: true, script: "zip -r ${deploymentPackageName} DEPLOY_COMMON")
		echo "Packaging in progress : ${zip}"
		
        sh '''
		
		cksum=`sha1sum ${deploymentPackageName} | awk -F" " '{print $1}'`		
		echo "Checksum for playbook -- $cksum"
		
		'''	
		
		
			uploadArtifactToGenericTemp { 
			    
				source = "${deploymentPackageName}"
				target = "${targetPackageName}"
			}
			
			moveArtifactFromGenericTemp { 
				source="${targetPackageName}"
				target="${genericReleaseDir}/${myprojectDir}/${myprojectProjectName}/${myprojectSystemName}/${targetPackageName}"
			} 
		        
    }

}
