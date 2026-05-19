pipeline {
    agent any

    stages {

        stage('Verify JMeter') {
            steps {
                bat '"C:\\Users\\phyne\\Desktop\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -v'
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '"C:\\Users\\phyne\\Desktop\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t practice.jmx -l results.jtl -e -o report'
            }
        }

        stage('Archive Reports') {
            steps {
                archiveArtifacts artifacts: 'report/**', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'JMeter Execution Completed'
        }
        success {
            echo 'Test Passed'
        }
        failure {
            echo 'Test Failed'
        }
    }
}