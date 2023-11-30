#!/usr/bin/env groovy

pipeline {

    agent any

    stages {
        stage('Build') {
           when {
               branch 'master'
           }
            steps {
                echo 'Building...'
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
