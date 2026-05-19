pipeline {
    agent any

    environment {
        JMETER = "C:\\Users\\phyne\\Desktop\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3\\bin\\jmeter.bat"
    }

    stages {

        stage('Run JMeter Test') {
            steps {
                bat "${JMETER} -n -t practice.jmx -l results.jtl -e -o report"
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'report/**'
            }
        }

        stage('Send Email Report') {
            steps {
                emailext (
                    subject: "JMeter Report - Build ${BUILD_NUMBER}",
                    body: "Check attached report from Jenkins build ${BUILD_NUMBER}",
                    to: "it.nirmaldangal@gmail.com",
                    attachmentsPattern: "report/**",
                    attachLog: true
                )
            }
        }
    }

    post {
        always {
            echo "Pipeline completed"
        }
    }
}