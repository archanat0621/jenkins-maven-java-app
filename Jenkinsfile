pipeline {
    agent any

    environment {
        GITHUB_CREDS = credentials('github-new-creds')
        MAVEN_HOME   = tool name: 'maven'
        PATH         = "${JAVA_HOME}\\bin;${PATH}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build & Deploy') {
            steps {
                configFileProvider([
                    configFile(
                        fileId: 'maven-github-settings',
                        variable: 'MAVEN_SETTINGS'
                    )
                ]) {
                    bat '''
                        echo ========================================
                        echo        BUILDING MAVEN PROJECT
                        echo ========================================

                        "%MAVEN_HOME%\\bin\\mvn.cmd" -s "%MAVEN_SETTINGS%" -B clean package

                        echo.
                        echo ========================================
                        echo       DEPLOYING TO GITHUB PACKAGES
                        echo ========================================

                        "%MAVEN_HOME%\\bin\\mvn.cmd" -s "%MAVEN_SETTINGS%" -B deploy
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Build and deployment to GitHub Packages completed successfully."
        }

        failure {
            echo "Pipeline failed. Check the console output for details."
        }
    }
}
