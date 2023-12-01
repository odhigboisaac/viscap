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
		eccho "----- build cmplted -----"
            }
        }
    }
}
