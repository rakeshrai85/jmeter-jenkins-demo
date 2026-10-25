pipeline {
    agent any

    parameters {
        string(name: 'THREADS',  defaultValue: '5',  description: 'Virtual users')
        string(name: 'RAMPUP',   defaultValue: '10', description: 'Ramp-up (s)')
        string(name: 'DURATION', defaultValue: '60', description: 'Duration (s)')
    }

    environment {
        JMETER_HOME = 'C:/apache-jmeter-5.6.3'
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                    if exist results rmdir /s /q results
                    if exist report rmdir /s /q report
                    mkdir results
                    "%JMETER_HOME%/bin/jmeter.bat" -n -t tests/sample.jmx -l results/results.jtl -e -o report -Jthreads=%THREADS% -Jrampup=%RAMPUP% -Jduration=%DURATION%
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'results/*.jtl', allowEmptyArchive: true
            publishHTML(target: [
                reportDir: 'report',
                reportFiles: 'index.html',
                reportName: 'JMeter Dashboard',
                keepAll: true,
                alwaysLinkToLastBuild: true
            ])
            perfReport sourceDataFiles: 'results/results.jtl',
                       errorFailedThreshold: 5,
                       errorUnstableThreshold: 2
        }
    }
}
