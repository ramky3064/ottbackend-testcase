pipeline {
    agent any

    tools {
        jdk 'JDK17'
        gradle 'Gradle'
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

        stage('Deploy') {
            steps {
                bat '''
                echo Deploying application...
                mkdir C:\\apps\\ott-backend 2>nul
                copy /Y build\\libs\\*.jar C:\\apps\\ott-backend\\ott-backend.jar
                start "" java -jar C:\\apps\\ott-backend\\ott-backend.jar
                '''
            }
        }

    }
}
