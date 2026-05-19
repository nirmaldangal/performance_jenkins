pipeline {
    agent any

    tools {
        jdk 'JDK17'
    }

    environment {
        JMETER_HOME = "C:\\Users\\phyne\\Desktop\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3"
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
                bat "\"%JMETER_HOME%\\bin\\jmeter.bat\" -v"
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                    \"%JMETER_HOME%\\bin\\jmeter.bat\" -n ^
                    -t %TEST_FILE% ^
                    -l %RESULTS_FILE% ^
                    -e -o %REPORT_DIR%
                """
            }
        }

        stage('Archive Reports') {
            steps {
                archiveArtifacts artifacts: 'results.jtl, report/**'
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML([
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true
                ])
            }
        }

        stage('Send Email Report') {
            steps {
                emailext(
                    subject: "JMeter Report - Build ${env.BUILD_NUMBER}",
                    body: """
                        <h2>JMeter Execution Completed</h2>
                        <p>Build: ${env.BUILD_NUMBER}</p>
                        <p>Status: ${currentBuild.currentResult}</p>
                    """,
                    to: "it.nirmaldangal@gmail.com",
                    attachmentsPattern: "report/**/index.html"
                )
            }
        }
    }

    post {
        always {
            echo "Pipeline finished"
        }
        success {
            echo "SUCCESS"
        }
        failure {
            echo "FAILED"
        }
    }
}