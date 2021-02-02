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
        agent {
            docker { image 'alpine:latest' }
        }
        always {
            sh 'curl -X POST --data-urlencode "payload={\"channel\": \"#general\", \"username\": \"webhookbot\", \"text\": \"This is posted to #general and comes from a bot named webhookbot.\", \"icon_emoji\": \":ghost:\"}" https://hooks.slack.com/services/T01LV3R105S/B01'
        }
    }
}