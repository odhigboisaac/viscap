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
        
         steps {
           withSonarQubeEnv(credentialsId: 'f225455e-ea59-40fa-8af7-08176e86507a', installationName: 'sonar-server-viscap') {
            sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar'
           }
          }     
        }      
    }
}
