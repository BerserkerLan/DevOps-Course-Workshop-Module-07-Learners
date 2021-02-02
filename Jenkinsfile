pipeline {
    agent none
     environment {
        DOTNET_CLI_HOME = "/tmp/DOTNET_CLI_HOME"
        SLACK_WEBHOOK_URL = credentials("SLACK_WEBHOOK_URL")
        // ACTIONS_ALLOW_UNSECURE_COMMANDS: true
    }
    stages {
        stage('Back-end') {
            agent {
                docker { image 'mcr.microsoft.com/dotnet/core/sdk:3.1' }
            }
            steps {
                sh 'dotnet build'
                sh 'dotnet test'
            }
        }
        stage('Front-end') {
            agent {
                docker { image 'node:14-alpine' }
            }
            steps {
                dir("DotnetTemplate.Web") {
                    sh 'npm install'
                    sh 'npm run build'
                    sh 'npm t'
                    sh 'npm run lint'
                }
            }
        }
    }
    post {
        always {
            slackSend color: "good", message: "Message from Jenkins Pipeline, I did it", token: "$SLACK_WEBHOOK_URL", channel: "@Rita Paiva" 
        }
    }
}