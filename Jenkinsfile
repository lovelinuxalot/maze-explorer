pipeline {
    agent any

    tools {
        jdk 'jdk8'
        maven 'maven3'
    }

    stages {
            //stage('SCM') {
		    //    steps {
			        //git 'https://github.com/lovelinuxalot/maze-explorer.git'
			//        sh 'git pull'
		    //    }
  	        //}

            stage('Build and test') {
		        steps {
        	    	    //buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean package cobertura:cobertura -Dcobertura.report.format=xml'
        	    	    //buildInfo = rtMaven.run pom: 'pom.xml', goals: '-Dmaven.test.failure.ignore -U clean validate compile test'
        	    	    sh 'mvn -Dmaven.test.failure.ignore=true -B clean validate compile test'
		        }
		        post {
		            success {
		                junit '**/target/*-reports/TEST-*.xml'
		                step([$class: 'JacocoPublisher', execPattern: '**/target/jacoco.exec'])
		            }
		        }
	        }

            stage('SonarQube analysis'){
                def scannerHome = tool 'sonar'
                        withSonarQubeEnv('sonarqube') {
                            sh "${scannerHome}/bin/sonar-scanner"
                }
            }

	        //stage('Unit Test') {
			//    steps {
			//
		    //        archive 'target/*.jar'
		    //        step([$class: 'JacocoPublisher', execPattern: '**/target/jacoco.exec'])
            //        //step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
			//    }
	        //}
    }
}
