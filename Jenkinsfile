pipeline {
    agent any

    stages {

        stage("Initial cleanup") {
            steps {
                cleanWs()
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
                sh 'php artisan migrate'
                sh 'php artisan db:seed'
                sh 'php artisan key:generate'
            }
        }

        stage('Execute Unit Tests') {
            steps {
                sh 'sudo ./vendor/bin/phpunit'
            }
        }

        stage('Code Analysis') {
            steps {
                sh 'mkdir -p build/logs'
                sh 'sudo chown -R jenkins:jenkins build/logs/'
                sh 'sudo chmod -R 777 build/logs/'
                sh 'phploc app/ --log-csv build/logs/phploc.csv'
                sh 'ls -l build/logs/' // Debug step to confirm CSV generation
                sh 'cat build/logs/phploc.csv || echo "CSV file not created!"'

                // Simplify CSV for plotting
                script {
                    sh '''
                    echo "Metric,Value" > build/logs/plot-loc.csv
                    grep '"Lines of Code (LOC)"' build/logs/phploc.csv | awk -F',' '{print "Lines of Code," $3}' >> build/logs/plot-loc.csv
                    '''
                }

                // Debug simplified CSV
                sh 'cat build/logs/plot-loc.csv || echo "Simplified CSV not created!"'
            }
        }

        stage('Plot Code Coverage Report') {
            steps {
                plot csvFileName: 'build/logs/plot-loc.csv',
                     group: 'Code Metrics',
                     title: 'Lines of Code',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false]]
            }
        }
    }
}
