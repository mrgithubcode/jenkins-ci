#!groovy
node('linux && ansible') {
    def jdkHome
    def mvnHome
    def artifactVersion
	def myprojectEnv
    def myprojectSystemName
	def myprojectProjectName		
	def targetPackageName
	def ansiblePackageName	
    
    stage('Source') {
        checkout scm
        notifyStash
    }
    
    stage('Build environment') {
        jdkHome = installTool "jdk1.8.0_91"
        mvnHome = installTool "apache-maven-3.3.9"
        myprojectSystemName = "mysystemname"
		myprojectProjectName = "Deployment"
		myprojectEnv = "dev"
		ansiblePackageName = "ansible_package.zip"		
		targetPackageName = "ansible_package.zip"					
    }
 
    stage("Packaging and publish to Artifact") {
	
	    echo "staring packaging process" 
		
		zip = sh(returnStdout: true, script: "zip ${ansiblePackageName} Playbooks/* group_vars/${myprojectEnv} ${myprojectEnv}_hosts")
		echo "Packaging in progress : ${zip}"
		
        sh '''
		
		cksum=`sha1sum ${ansiblePackageName} | awk -F" " '{print $1}'`		
		echo "Checksum for playbook -- $cksum"
		
		'''
		
			uploadArtifactToGenericTemp { 
			    
				source = "${ansiblePackageName}"
				target = "${targetPackageName}"
			}
		        
    }

}
