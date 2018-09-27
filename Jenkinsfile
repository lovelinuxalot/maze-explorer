node {
	    	        
	stage('SCM') {
    		git 'https://github.com/lovelinuxalot/maze-explorer.git'
  	}
	
        stage('Build') {
        	//buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean package cobertura:cobertura -Dcobertura.report.format=xml'
		//def mvnHome = tool name: 'maven3', type: 'maven'
		//sh "${mvnHome}/bin/mvn clean install -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true -B -V"
		mvn 'clean install -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true -B -V'
	}
	
	unitTest()
	
	stage('All Test') {
        	junit '**/target/*-reports/TEST-*.xml'
		//mvn "org.jacoco:jacoco-maven-plugin:prepare-agent install -Dmaven.test.failure.ignore=true -Pcoverage-per-test"
		//step([$class: 'JacocoPublisher', execPattern: '**/target/jacoco.exec'])
		
                //step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
	}
	
	
	//stage('SonarQube analysis') {
    		// requires SonarQube Scanner 2.8+
	//	def mvnHome = tool name: 'maven3', type: 'maven'
    	//	withSonarQubeEnv('sonar') {
	//		sh "${mvnHome}/bin/mvn sonar:sonar"
    	//	}
  	//}
	
	stage('SonarQube analysis'){
		mvn "org.jacoco:jacoco-maven-plugin:prepare-agent install -Dmaven.test.failure.ignore=true -Pcoverage-per-test"
		def scannerHome = tool 'scanner'
                withSonarQubeEnv('sonar') {
                    sh "${scannerHome}/bin/sonar-scanner"
		}
	}
	
	stage('sleep stage'){
		sh "sleep 10"
	}
	
	stage("SonarQube Quality Gate") { 
        	timeout(time: 1, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
            		def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
            		if (qg.status != 'OK') {
                		echo "Pipeline aborted due to quality gate failure: ${qg.status}"
            		} else {
				buildAndPushToArtifactory()
            		}    
		}
    	}
}

def mvn(args) {
    withMaven(
        maven: 'maven3',
        ) {
        // Run the maven build
        sh "mvn $args -Dmaven.test.failure.ignore"
    }
}

def buildAndPushToArtifactory() {
	// Get Artifactory server instance, defined in the Artifactory Plugin administration page.
	def server = Artifactory.server "artifactory"
	def buildInfo = Artifactory.newBuildInfo()
	buildInfo.env.capture = true
	// Create an Artifactory Maven instance.
	def rtMaven = Artifactory.newMavenBuild()
	// Tool name from Jenkins configuration
	rtMaven.tool = "maven3"
	// Set Artifactory repositories for dependencies resolution and artifacts deployment.
	rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
	rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
	rtMaven.run pom: 'pom.xml', goals: 'install', buildInfo: buildInfo
	server.publishBuildInfo buildInfo
}

def unitTest() {
	stage('Unit tests') {
			def mvnHome = tool name: 'maven3', type: 'maven'
			mvn 'test -B -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true'
			if (currentBuild.result == "UNSTABLE") {
				sh "exit 1"
			}
	}
}
