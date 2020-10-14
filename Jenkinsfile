pipeline {
      agent any
      environment {
           CHKP_CLOUDGUARD_ID = credentials("CHKP_CLOUDGUARD_ID")
           CHKP_CLOUDGUARD_SECRET = credentials("CHKP_CLOUDGUARD_SECRET")
           SG_CLIENT_ID = credentials("SG_CLIENT_ID")
           SG_SECRET_KEY = credentials("SG_SECRET_KEY")
        }
        
  stages {
          
         stage('Clone Github repository') {
            
    
           steps {
              
             checkout scm
           
             }
  
          }
  stage('Scan source code before containerizing App') {    
           
            steps {
             script {      
              try {
                    sh 'chmod +x shiftleft' 
                    sh './shiftleft code-scan -s ./'
              }    catch (Exception e) {
    
                 echo "Request for Approval"  
                  }
            }
      }
    }
  stage('Code approval request') {
     
           steps {
             script {
               def userInput = input(id: 'confirm', message: 'This code contains vulnerabilities. Would you still like to continue?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Approve Code to Proceed', name: 'approve'] ])
              }
            }
          }
 
   stage('Build Docker Image') {    
           
            steps {
             script {      
           
                    app = docker.build("michaelbraunbass/vulnerablewebapp") 

        }
      } 
   }
  stage('Scan container before pushing to Dockerhub') {    
           
            steps {
             script {      
              try {
                    sh 'docker save michaelbraunbass/vulnerablewebapp -o vwa.tar' 
                    sh './shiftleft image-scan -i ./vwa.tar'
              }    catch (Exception e) {
    
                 echo "Request for Approval"  
                  }
            }
      }
    }
  stage('Dockerhub Approval Request') {
     
           steps {
             script {
               def userInput = input(id: 'confirm', message: 'This containers contains vulnerabilities. Push to Dockerhub?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Approve Code to Proceed', name: 'approve'] ])
              }
            }
          }  
        
   stage('Deploy App to Dockerhub') {
     
           steps {
             script {
                   docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login'){
                         app.push("latest")
              }
            }
          }          
        
  }
}
