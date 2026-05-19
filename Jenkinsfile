pipeline {
    agent any

    tools {
        // Must match Jenkins → Manage Jenkins → Tools → JDK name
        jdk 'JDK17'
    }

    environment {
        JMETER_HOME = "C:\\Users\\phyne\\Desktop\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin"
        REPORT_DIR = "report"
        TEST_FILE = "practice.jmx"
        RESULTS_FILE = "results.jtl"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/nirmaldangal/performance_jenkins.git'
            }
        }

        stage('Clean Workspace') {
            steps {
                bat 'if exist report rmdir /s /q report'
                bat 'if exist results.jtl del /f /q results.jtl'
            }
        }

        stage('Verify JMeter') {
            steps {
                bat '"%JMETER_HOME%\\jmeter.bat" -v'
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                    "%JMETER_HOME%\\jmeter.bat" -n ^
                    -t %TEST_FILE% ^
                    -l %RESULTS_FILE% ^
                    -e -o %REPORT_DIR%
                """
            }
        }

        stage('Archive Reports') {
            steps {
                archiveArtifacts artifacts: 'results.jtl, report/**', fingerprint: true
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML([
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }

        stage('Send Email Report') {
            steps {
                emailext(
                    subject: "JMeter Test Report - Build #${env.BUILD_NUMBER}",
                    body: """
                        <h2>JMeter Test Completed</h2>
                        <p><b>Build Number:</b> ${env.BUILD_NUMBER}</p>
                        <p><b>Status:</b> ${currentBuild.currentResult}</p>
                        <p>Find attached HTML report.</p>
                    """,
                    to: "your_email@gmail.com",
                    attachmentsPattern: "report/**/index.html"
                )
            }
        }
    }

    post {
        success {
            echo "Pipeline Success"
        }

        failure {
            echo "Pipeline Failed"
        }

        always {
            echo "JMeter Test Execution Completed"
        }
    }
}