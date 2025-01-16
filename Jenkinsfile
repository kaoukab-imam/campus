pipeline {
    agent any

    tools {
        nodejs 'node'  // Le nom que vous avez donné à l'installation dans Global Tool Configuration
    }

    environment {
        PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD = 1
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Playwright and Browsers') {
            steps {
                bat 'npm install playwright'
                bat 'npx playwright install'
            }
        }

        stage('Run Playwright Tests') {
            steps {
                script {
                    try {
                        bat 'npx playwright test'
                    } catch (Exception e) {
                        echo "Tests failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'playwright-report/**', fingerprint: true
            
            // Publier le rapport HTML Playwright
            publishHTML(target: [
                reportName: 'Playwright Test Report',
                reportDir: 'playwright-report',
                reportFiles: 'index.html',
                alwaysLinkToLastBuild: true,
                keepAll: true
            ])
        }

        failure {
            echo 'Tests failed. Please check the Playwright report for details.'
        }
    }
}
