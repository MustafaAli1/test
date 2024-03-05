pipeline {
    agent any
    environment {
        MSG = ''
        AUTHOR=''
        MSTEAMS_HOOK='https://andpercentcom.webhook.office.com/webhookb2/c7db730d-9e39-4bfd-832a-1aacb2d5f9ec@a8d4876b-beb2-4dd3-82ef-64221ad445ed/JenkinsCI/8cd9efbc90ca4501bade846a343af335/580739c1-d533-498f-9496-e333536ae5e9'
    }
    stages {
        stage('Clone repository') {
            steps {
            checkout scm
            }
        }
        stage('get_commit_details') {
            steps {
             script {
                    MSG = sh ( script: 'git log -1 --pretty=%B', returnStdout: true ).trim()
                    AUTHOR = sh ( script: 'git log -1 --pretty=%ce', returnStdout: true ).trim()
              }
           }
         }



        
        stage('Deploy to Staging') {
            steps{
                script{
                    echo scm.branches[0].name
                    if (scm.branches[0].name == "main") {
                    
                   
                     sshagent(credentials: ['iwish-Id']) {
               
               
                           sh 'ssh -o StrictHostKeyChecking=no ubuntu@157.241.45.181 "cd /var/www/html/test && git pull && bash -ic '/home/ubuntu/.nvm/versions/node/v20.11.0/bin/pm2 restart all'"
                        
                          
                       }

                }
                   else { echo "No commit to Staging"}
                }
            }
        }

    }
    
    
    post{
        success {
               office365ConnectorSend (
   		 status: "Build Successful",
    		 webhookUrl: "${MSTEAMS_HOOK}",
    		 color: '00ff00',
    		 message: "Build Successful : ${JOB_NAME} - ${BUILD_DISPLAY_NAME}<br>Pipeline duration: ${currentBuild.durationString}<br>Author Name: ${AUTHOR}<br>Commit Message: ${MSG}"
               )
        }
        failure {
               office365ConnectorSend (
   		 status: "Build Unsuccessful",
    		 webhookUrl: "${MSTEAMS_HOOK}",
    		 color: 'ff0000',
    		 message: "Build Unsuccessful : ${JOB_NAME} - ${BUILD_DISPLAY_NAME}<br>Pipeline duration: ${currentBuild.durationString}<br>Author Name: ${AUTHOR}<br>Commit Message: ${MSG}"
               )
            }
        unstable {
               office365ConnectorSend (
   		 status: "Build Unstable",
    		 webhookUrl: "${MSTEAMS_HOOK}",
    		 color: 'ffff00',
    		 message: "Build Unstable : ${JOB_NAME} - ${BUILD_DISPLAY_NAME}<br>Pipeline duration: ${currentBuild.durationString}<br>Author Name: ${AUTHOR}<br>Commit Message: ${MSG}"
               )
            }
       }
}
