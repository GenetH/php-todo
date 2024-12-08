pipeline {
    agent any

    stages {
        stage("Initial Cleanup") {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/GenetH/php-todo'
            }
        }

        stage('Prepare Workspace') {
            steps {
                script {
                    sh '''
                        # Create necessary directories and log file
                        mkdir -p storage/app storage/framework/{cache,sessions,views} storage/logs bootstrap/cache
                        touch storage/logs/laravel.log

                        # Set permissions
                        sudo chmod -R 775 storage bootstrap/cache
                        sudo chown -R www-data:www-data storage bootstrap/cache
                    '''
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh '''
                        # Install PHP dependencies
                        composer install
                    '''
                }
            }
        }

        stage('Laravel Setup') {
            steps {
                script {
                    sh '''
                        # Run Laravel commands
                        php artisan key:generate
                        php artisan migrate --force
                        php artisan db:seed --force
                        php artisan config:cache
                        php artisan route:cache
                        php artisan view:cache
                    '''
                }
            }
        }

        stage('Execute Unit Tests') {
            steps {
                script {
                    sh '''
                        # Run PHP unit tests
                        ./vendor/bin/phpunit
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for more details.'
        }
    }
}
