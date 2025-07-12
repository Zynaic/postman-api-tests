pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Run Postman Tests') {
            steps {
                script {
                    try {
                        bat 'npm test'
                    } catch (Exception e) {

                        // Mark build as unstable and allow archive stage to run
                        currentBuild.result = 'UNSTABLE'
                        echo "Tests failed: ${e.getMessage()}"
                    }
                }
            }
        }

        stage('Archive Reports') {
            steps {
                // Archive reports regardless of test result
                archiveArtifacts artifacts: 'reports/**/*', allowEmptyArchive: true

                // Publish HTML report to Jenkins UI
                publishHTML([
                    reportName: 'Newman Test Report',
                    reportDir: 'reports',
                    reportFiles: 'report.html',
                    allowMissing: true,
                    alwaysLinkToLastBuild: true,
                    keepAll: true
                ])
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'All tests passed successfully!'
        }
        unstable {
            echo 'Some tests failed. Check the Newman Test Report.'
        }
        failure {
            echo 'Build failed. Check the console log and reports.'
        }
    }
}
