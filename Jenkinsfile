pipeline {
      agent any
      environment {
           SG_CLIENT_ID = credentials("SG_CLIENT_ID")
           SG_SECRET_KEY = credentials("SG_SECRET_KEY")
           CLOUDGUARD_ID = credentials("CLOUDGUARD_ID")
           CLOUDGUARD_KEY = credentials("CLOUDGUARD_KEY")
        }
        
  stages {
          
         stage('Clone Github repository') {
            
    
           steps {
              
             checkout scm
           
             }
  
          }
          
    stage('ShiftLeft Code Scan') {   
       steps {   
                   
         script {      
              try {

             
                
            
                sh 'chmod +x shiftleft' 

                sh './shiftleft sourceguard -s .'
           
               } catch (Exception e) {
    
                 echo "Request for Approval"  
                  }
              }
            }
         }
         
     stage('Code approval request') {
     
           steps {
             script {
               def userInput = input(id: 'confirm', message: 'Do you Approve to use this code?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Approve Code to Proceed', name: 'approve'] ])
              }
            }
          }
           
           
          stage('webapp Docker image Build and scan prep') {
             
            steps {

              sh 'docker build -t dhouari/webapp .'
              sh 'docker save dhouari/webapp -o webapp.tar'
              
             } 
           }
        
           
       stage('ShiftLeft Container Image Scan') {    
           
            steps {
                script {      
              try {
         
                    sh './shiftLeft image-scan -i webapp.tar'
                   } catch (Exception e) {
    
                 echo "Request for Approval"  
                  }
                }  
             }
          }
            
       stage('Code approval request') {
     
           steps {
             script {
               def userInput = input(id: 'confirm', message: 'Do you Approve to use this container image?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Approve docker image to Proceed', name: 'approve'] ])
              }
            }
          }
        
      stage('Terraform config policy Scan') {    
           
            steps {
         
                    sh './shiftleft iac-assessment -l S3Bucket should have encryption.serverSideEncryptionRules -p "./terraform"'
                    
              }
            }
  } 
}

