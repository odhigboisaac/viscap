pipeline {
    agent {
        node {
            label 'maven'
        }
    }

environment {
   PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
   }

    stages {
        stage('build begin') {
            steps {
	        echo "----- build started -----"
                sh 'mvn clean deploy'
		echo "----- build cmplted -----"
            }
        }
        stage('SonarQube analysis') {
        environment {
         scannerHome = tool 'sonar-scanner-viscap'
          }
       steps {
        withSonarQubeEnv('sonar-server-viscap') { 
          sh "${scannerHome}/bin/sonar-scanner"
         }
  
         
    }
}
