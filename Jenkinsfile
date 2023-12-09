def registry = 'https://viscap.jfrog.io/'
def imageName = 'viscap.jfrog.io/viscap-docker-local/viscap'
def version   = '2.1.2'

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
      
    // stage("Quality Gate"){
    //     steps {
    //      script {
    //         timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
    //          def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    //          if (qg.status != 'OK') {
    //            error "Pipeline aborted due to quality gate failure: ${qg.status}"
    //           }
    //          }
    //         }
    //        }
    // }
        
    stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog_cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
                 }
              }   
      } 

    stage(" Docker Build ") {
       steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
           }
         }
    }
    stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'jfrog_cred'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
              }
          }
      } 
//    stage ('deploy with sh') { 
//      steps {
//       script {
//         sh './deploy.sh'
//         }
//        }
//      }
    stage("deploy with helm") { 
     steps {
       script {
           echo "<-------- Helm deployment started -------->"
           sh 'helm install viscaphelm-0.1.0.tgz"
           echo "<---------helm deploy complt -------->"     
            }
          }
        }  

  }
}
