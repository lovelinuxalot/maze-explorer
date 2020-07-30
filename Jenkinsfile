pipeline {
    agent any

    tools {
        jdk 'jdk8'
        maven 'maven3'
    }

    stages {
            stage('SCM') {
		        steps {
			        //git 'https://github.com/lovelinuxalot/maze-explorer.git'
			        sh 'git pull'
		        }
  	        }

            stage('Build and test') {
		        steps {
        	    	    //buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean package cobertura:cobertura -Dcobertura.report.format=xml'
        	    	    //buildInfo = rtMaven.run pom: 'pom.xml', goals: '-Dmaven.test.failure.ignore -U clean validate compile test'
        	    	    sh 'maven -B clean validate compile test'
		        }
	        }

	        stage('Unit Test') {
			    steps {
				    junit '**/target/*-reports/TEST-*.xml'
		            archive 'target/*.jar'
		            step([$class: 'JacocoPublisher', execPattern: '**/target/jacoco.exec'])
                    //step([$class: 'CoberturaPublisher', coberturaReportFile: 'target/site/cobertura/coverage.xml'])
			    }
	        }
    }
}
