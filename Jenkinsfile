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
                sh 'mvn clean deploy -Dmaven.skip=true'
		echo "----- build cmplted -----"
            }
        } 

	stage ('Unit test') {
	 steps {
	   echo"--------Unit test started-------"
           sh 'mvn surefire-report:report'
	   echo"--------Unit test compltedd-----"
	 }
	}
        stage('SonarQube analysis') {
        environment {
         scannerHome = tool 'viscap-sonar-scanner'
          }
        steps {
         withSonarQubeEnv('viscap-sonarqube-server') { 
          sh "${scannerHome}/bin/sonar-scanner"
          }      
         }
        }  
      
       stage("Quality Gate"){
        steps {
         script {
            timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
             def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
             if (qg.status != 'OK') {
               error "Pipeline aborted due to quality gate failure: ${qg.status}"
              }
             }
            }
           }
         }


   }
}
