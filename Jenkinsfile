pipeline {
    agent any

    options {
        timestamps()
    }

    tools {
        jdk 'JDK21'
        maven 'Maven3'
    }

    environment {
        GITHUB_CREDS = credentials('github-pat')
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Verify Tools') {
            steps {
                bat 'java -version'
                bat 'mvn -version'
                bat 'git --version'
            }
        }

        stage('Build, Test and Publish') {
            steps {
                configFileProvider([
                    configFile(
                        fileId: 'maven-github-settings',
                        variable: 'MAVEN_SETTINGS'
                    )
                ]) {
                    bat '''
                        echo ========================================
                        echo BUILDING AND TESTING MAVEN PROJECT
                        echo ========================================

                        mvn -s "%MAVEN_SETTINGS%" -B clean deploy
                    '''
                }
            }
        }
    }

    post {
        always {
            junit testResults: 'target/surefire-reports/*.xml',
                  allowEmptyResults: true

            archiveArtifacts artifacts: 'target/*.war',
                             fingerprint: true,
                             allowEmptyArchive: true
        }

        success {
            echo 'Build and publication to GitHub Packages completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the console output for details.'
        }
    }
}
