def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
def getBuildUser() {
    return currentBuild.rawBuild.getCause(Cause.UserIdCause).getUserId()
}
pipeline {
    agent any
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
        // agent {
        //     docker { image 'alpine:latest' }
        // }
        always {
            script {
                BUILD_USER = getBuildUser()
            }
            echo 'Hi'
            slackSend channel: "#general", color: COLOR_MAP[currentBuild.currentResult], message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} by ${BUILD_USER}\n More info at: ${env.BUILD_URL}"
        }
    }
}