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
             sh 'mkdir -p storage/app storage/framework/{cache,sessions,views}  bootstrap/cache'
             sh 'sudo chmod -R 775 storage bootstrap/cache'
             sh 'sudo chmod -R 775 storage storage/logs'
             sh 'sudo chown -R www-data:www-data storage bootstrap/cache' 
             sh 'composer install'
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