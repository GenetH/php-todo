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
                
                sh 'phploc app/ --log-csv build/logs/phploc.csv'
                sh 'ls -l build/logs/' // Debug step
                sh 'cat build/logs/phploc.csv || echo "CSV file not created!"'

                // Simplify CSV for plotting
                script {
                    sh '''
                    echo "Metric,Value" > build/logs/plot-loc.csv
                    grep "Lines of Code (LOC)" build/logs/phploc.csv | awk -F',' '{print "Lines of Code," $3}' >> build/logs/plot-loc.csv
                    '''
                }
            }
        }

        stage('Plot Code Coverage Report') {
            steps {
                plot csvFileName: 'build/logs/plot-loc.csv',
                     group: 'Code Metrics',
                     title: 'Lines of Code',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Metric,Value']]
            }
        }
    }
}
