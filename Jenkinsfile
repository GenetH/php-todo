pipeline {
    agent any

    stages {

        stage("Initial cleanup") {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                    // Fix permissions
                    sh 'chmod -R 775 .'
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/GenetH/php-todo'
            }
        }

        stage('Prepare Dependencies') {
            steps {
                sh 'mv .env.sample .env || echo ".env.sample not found, skipping rename"'
                sh 'composer install -vvv'
                sh 'php artisan config:clear'
                sh 'php artisan cache:clear'
                sh 'php artisan migrate -vvv'
                sh 'php artisan db:seed -vvv'
                sh 'php artisan key:generate -vvv'
            }
        }

        stage('Execute Unit Tests') {
            steps {
                sh './vendor/bin/phpunit --verbose'
            }
        }

    }
}
