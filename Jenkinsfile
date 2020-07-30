pipeline {
    agent any

    tools {
        jdk 'jdk8'
        maven 'maven3'
    }

    stages {
            stage('SCM') {
    		    git 'https://github.com/lovelinuxalot/maze-explorer.git'
  	        }
        
            stage('Build and test') {
        	    //buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean package cobertura:cobertura -Dcobertura.report.format=xml'
        	    buildInfo = rtMaven.run pom: 'pom.xml', goals: '-Dmaven.test.failure.ignore -U clean validate compile test'
	        }
	
	        stage('Unit Test') {
        	    junit '**/target/*-reports/TEST-*.xml'
		        archive 'target/*.jar'
		        step([$class: 'JacocoPublisher', execPattern: '**/target/jacoco.exec'])
		
                //step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
	        }

    }
}
