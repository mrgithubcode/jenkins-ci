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
        notifyStash
    }

    stage('Get Artifact details') {
        pom = readMavenPom file: 'myproject/pom.xml'
        artifactVersion = pom.version 
        echo "artifact Version: ${artifactVersion}"
    }
    
    stage('Build environment') {
        jdkHome = installTool "jdk1.8.0_91"
        mvnHome = installTool "apache-maven-3.3.9"
        myprojectSystemName = "mysystemname"
		myprojectProjectName = "myproject"
		myprojectEnv = "sit"
    }  
	
    stage("Archive and Deploy") {
        ansiblePlaybook colorized: false, credentialsId: 'IF_sit_Deployer', installation: 'Ansible 2.1', inventory: 'Playbooks/myprojecthosts',limit: "${myprojectEnv}", playbook: 'Playbooks/deploy.yml', sudoUser: null , extraVars: [artifactVersion: "${artifactVersion}", myprojectSystemName: "${myprojectSystemName}", myprojectProjectName:"${myprojectProjectName}", myprojectEnv:"${myprojectEnv}"]
    }
}
