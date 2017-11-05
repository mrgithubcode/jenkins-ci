#!groovy
node('linux && ansible') {
    def jdkHome
    def mvnHome
    def artifactVersion
	def myprojectEnv
    def myprojectSystemName
	def myprojectProjectName		
    
    stage('Source') {
        checkout scm
        notifyBitBucket
    }
    
    stage('Build environment') {
        jdkHome = installTool "jdk1.8.0_91"
        mvnHome = installTool "apache-maven-3.3.9"
        myprojectSystemName = "SYSTEMNAME"
		myprojectProjectName = "Deployment"
		myprojectEnv = "sit"				
    }
	
	stage("Archive and Deploy") {
		echo "starting deployment(artifacts download)"
        ansiblePlaybook colorized: false, credentialsId: 'credentialsId', installation: 'Ansible 2.1', inventory: "${myprojectEnv}_hosts",limit: "${myprojectEnv}", playbook: 'Playbooks/artifact_download.yml', sudoUser: null , extraVars: [ myprojectSystemName: "${myprojectSystemName}", myprojectProjectName:"${myprojectProjectName}", myprojectEnv:"${myprojectEnv}"]
    }
    

}
