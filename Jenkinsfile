pipeline {
    agent any

    tools {
        jdk 'jdk-21'
        gradle 'Gradle'
    }

    environment {
        SONAR_PROJECT_KEY  = 'ott-backend'
        SONAR_PROJECT_NAME = 'ott-backend'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'gradle clean build'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat '''
                    gradle sonar ^
                      -Dsonar.projectKey=ott-backend ^
                      -Dsonar.projectName=ott-backend ^
                      -Dsonar.host.url=http://localhost:9000 ^
                      -Dsonar.token=%sonar-auth-token%
                    '''
                }
            }
        }
    }
}
