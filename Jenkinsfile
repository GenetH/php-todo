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

      }
    }

        stage('Plot Code Coverage Report') {
            steps {
                plot csvFileName: 'plot-loc.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Executable Lines of Code']]

                plot csvFileName: 'plot-structures.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces']]

                plot csvFileName: 'plot-avg-length.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC)']]

                plot csvFileName: 'plot-complexity.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity']]

                plot csvFileName: 'plot-classes.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes']]

                plot csvFileName: 'plot-methods.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods']]

                plot csvFileName: 'plot-constants.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants']]

                plot csvFileName: 'plot-testing.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods']]

                plot csvFileName: 'plot-tlloc.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length LLOC,Functions Length LLOC']]

                plot csvFileName: 'plot-functions.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions']]

                plot csvFileName: 'plot-objects.csv', 
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants']]
            }
        }

    }
}
