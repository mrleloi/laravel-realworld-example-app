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

        stage('Create Dockerfile') {
            steps {
                writeFile file: 'Dockerfile', text: '''
FROM php:7.4-fpm

# Install required extensions and dependencies
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libzip-dev \
    unzip \
    git \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install pdo_mysql zip

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set working directory
WORKDIR /var/www/html

# Copy application files
COPY . /var/www/html

# Install dependencies
RUN composer install --no-interaction --no-dev --optimize-autoloader

# Set permissions
RUN chown -R www-data:www-data /var/www/html/storage
RUN chown -R www-data:www-data /var/www/html/bootstrap/cache
'''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t leloimr/php:laravel-7.4-fpm:${env.GIT_COMMIT_SHORT} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'b0e49bf4-e483-40ba-97a4-7ae2333da792', variable: 'DOCKERHUB_TOKEN')]) {
                    sh "echo '${DOCKERHUB_TOKEN}' | docker login -u leloimr --password-stdin"
                    sh "docker push leloimr/php:laravel-7.4-fpm:${env.GIT_COMMIT_SHORT}"
                }
            }
        }

        stage('Deploy Laravel Application') {
            steps {
                // Replace 'ssh-credentials-id' with your SSH credentials ID in Jenkins
                sshagent(['a9034061-bd88-4e15-9efb-6d9645d3228b']) {
                    // Replace 'your-deployment-server' with your server's IP or domain
                    sh "ssh 14.225.192.203 'docker pull leloimr/php:laravel-7.4-fpm:${env.GIT_COMMIT_SHORT}'"
                    sh "ssh 14.225.192.203 'docker-compose down && docker-compose up -d --force-recreate'"
                }
            }
        }
    }

    post {
        failure {
            // Configure notifications if required, e.g., email, Slack, etc.
        }
    }
}
