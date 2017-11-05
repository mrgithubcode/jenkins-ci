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
        pom = readMavenPom file: 'myprojectCore/pom.xml'
        artifactVersion = pom.version 
        echo "artifact Version: ${artifactVersion}"
    }
    
    stage('Build environment') {
        jdkHome = installTool "jdk1.8.0_91"
        mvnHome = installTool "apache-maven-3.3.9"
        myprojectSystemName = "mysystemname"
		myprojectProjectName = "myprojectCore"
		myprojectEnv = "sit"
    }

	stage('Build') {
        catchError {
            withEnv(["JAVA_HOME=${jdkHome}", "MAVEN_HOME=${mvnHome}", "PATH+MAVEN=${mvnHome}/bin"]) {
                artifactoryMaven {
                    credentials_id = 'jenkins_resolver'
                    pom = 'myprojectCore/pom.xml'
                    goals = 'clean install'
                }
            }
          }
        notifyStash
    }    
	
    stage("Archive and Deploy") {
        ansiblePlaybook colorized: false, credentialsId: 'credentials_id', installation: 'Ansible 2.1', inventory: 'Playbooks/myprojecthosts',limit: "${myprojectEnv}", playbook: 'Playbooks/build.yml', sudoUser: null , extraVars: [artifactVersion: "${artifactVersion}", myprojectSystemName: "${myprojectSystemName}", myprojectProjectName:"${myprojectProjectName}", myprojectEnv:"${myprojectEnv}"]
    }
}
