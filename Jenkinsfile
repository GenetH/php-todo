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
            sudo chown -R jenkins:jenkins build/

      }
    }

          stage('Plot Code Coverage Report') {
            steps {
                plot csvFileName: 'plot-loc.csv',
                     group: 'Code Metrics',
                     title: 'Lines of Code',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Executable Lines of Code']]

                plot csvFileName: 'plot-structures.csv',
                     group: 'Code Metrics',
                     title: 'File Structures',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces']]

                plot csvFileName: 'plot-avg-length.csv',
                     group: 'Code Metrics',
                     title: 'Average Length',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC)']]

                plot csvFileName: 'plot-complexity.csv',
                     group: 'Code Metrics',
                     title: 'Complexity Metrics',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity']]

                plot csvFileName: 'plot-classes.csv',
                     group: 'Code Metrics',
                     title: 'Class Metrics',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes']]

                plot csvFileName: 'plot-methods.csv',
                     group: 'Code Metrics',
                     title: 'Methods Metrics',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods']]

                plot csvFileName: 'plot-constants.csv',
                     group: 'Code Metrics',
                     title: 'Constants',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants']]

                plot csvFileName: 'plot-testing.csv',
                     group: 'Code Metrics',
                     title: 'Testing Metrics',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods']]

                plot csvFileName: 'plot-tlloc.csv',
                     group: 'Code Metrics',
                     title: 'Logical Lines of Code',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length LLOC,Functions Length LLOC']]

                plot csvFileName: 'plot-functions.csv',
                     group: 'Code Metrics',
                     title: 'Functions Metrics',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions']]

                plot csvFileName: 'plot-objects.csv',
                     group: 'Code Metrics',
                     title: 'Object Metrics',
                     style: 'line',
                     csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants']]
            }
        }
    }
}