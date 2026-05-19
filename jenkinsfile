pipeline {
    agent any

    tools {
        jdk 'JDK17'
    }

    environment {
        JMETER_HOME = '/opt/apache-jmeter-5.6.3'
        TEST_PLAN = 'practice.jmx'
        RESULTS_FILE = 'results.jtl'
        REPORT_FOLDER = 'HTMLReport'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/your-repo/jmeter-project.git'
            }
        }

        stage('Verify JMeter') {
            steps {
                sh '''
                    echo "Checking JMeter Version"
                    ${JMETER_HOME}/bin/jmeter -v
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                sh '''
                    mkdir -p reports

                    ${JMETER_HOME}/bin/jmeter \
                    -n \
                    -t ${TEST_PLAN} \
                    -l reports/${RESULTS_FILE} \
                    -e \
                    -o reports/${REPORT_FOLDER}
                '''
            }
        }

        stage('Archive Reports') {
            steps {
                archiveArtifacts artifacts: 'reports/**', fingerprint: true
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'reports/HTMLReport',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report'
                ])
            }
        }
    }

    post {
        always {
            echo 'JMeter Test Execution Completed'
        }

        success {
            echo 'Performance Test Passed'
        }

        failure {
            echo 'Performance Test Failed'
        }
    }
}