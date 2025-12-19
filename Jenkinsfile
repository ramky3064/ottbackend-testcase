pipeline {
    agent any

    tools {
        jdk 'jdk-17'
        gradle 'Gradle'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ramky3064/ottbackend.git',
                    credentialsId: 'github-pat'
            }
        }

        stage('Build & Test') {
            steps {
                bat 'gradle clean test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('LocalSonar') {
                    bat """
                    gradle sonar \
                    -Dsonar.projectKey=my-project \
                    -Dsonar.projectName=my-project
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Artifact') {
            steps {
                bat 'gradle build'
            }
        }

        stage('Deploy with Ansible') {
            steps {
                bat '''
                cd ansible
                ansible-playbook ansible/deploy.yml
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}
