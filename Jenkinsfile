pipeline {
    agent any

    stages {

        stage("Initial cleanup") {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
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
                sh 'mv .env.sample .env'
                sh 'composer install'
                sh 'mkdir -p storage/framework/sessions storage/framework/cache storage/framework/views'
                sh 'chmod -R 775 storage bootstrap/cache'
                sh 'php artisan migrate'
                sh 'php artisan db:seed'
                sh 'php artisan key:generate'
            }
        }

       stage('Execute Unit Tests') {
      steps {
             sh './vendor/bin/phpunit'
      }

    }

    }
}
