pipeline {
    agent any

    tools {
        jdk 'jdk8'
        maven 'maven3'
    }

    stages {
            stage('SCM') {
		        steps {
			        git 'https://github.com/lovelinuxalot/maze-explorer.git'
		        }
  	        }
    }
}
