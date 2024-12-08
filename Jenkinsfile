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
                sh '''
                    # Move environment file
                    mv .env.sample .env

                    # Create required directories
                    mkdir -p storage/app storage/framework/{cache,sessions,views} storage/logs bootstrap/cache

                    # Set permissions
                    sudo chmod -R 775 storage bootstrap/cache
                    sudo chown -R www-data:www-data storage bootstrap/cache
                '''

              
                sh '''
                    composer install
                    php artisan migrate --force
                    php artisan db:seed --force
                    php artisan key:generate
                '''
            }
        }

        stage('Execute Unit Tests') {
            steps {
                sh './vendor/bin/phpunit'
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed!'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
