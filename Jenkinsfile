pipeline {
    agent {
        kubernetes {
            containerTemplate {
                name 'shell'
                image 'python:3.8'
                command 'sleep'
                args 'infinity'
            }
            defaultContainer 'shell'
        }
    }
    stages {
        stage('Setup environment') {            
            steps {
                // Ensure that Python 3 and Pip are installed
                // then install the project required modules
                sh 'python3 -m pip install twine --trusted-host pypi.org'
                sh 'python3 -m pip install -r requirements.txt --trusted-host pypi.org --upgrade'
            }
        }
        stage('Produce output') {
            steps {
                // Produce the unit test reports
                sh 'python3 -m xmlrunner'
                // Produce the coverage reports
                sh 'nose2 -v --with-coverage --coverage-report.xml'
            }
            post {
                always {
                    // Always post test and coverage reports to Jenkins
                    junit 'test-reports/*.xml'
                    cobertura coberturaReportFile: 'coverage.xml'
                }
                success {
                    // build source and binary wheels
                    sh 'python3 setup.py sdist bdist_wheel'
                    // Push these somewhere if you want...
                }
            }
        }
    }
}
