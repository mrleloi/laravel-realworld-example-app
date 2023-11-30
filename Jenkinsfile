pipeline {
    agent any

    environment {
        GIT_COMMIT_SHORT = "${sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/mrleloi/laravel-realworld-example-app'
            }
        }

        stage('Build') {
            steps {
                sh "cd /var/www/html/laravel_base"
                sh "git pull https://github.com/mrleloi/laravel-realworld-example-app"
            }
        }
    }

    post {
        failure {
            // Configure notifications if required, e.g., email, Slack, etc.
        }
    }
}
