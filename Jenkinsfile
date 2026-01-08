pipeline {
    agent any

    tools {
        jdk 'jdk-21'   // Must match Jenkins Global Tool Configuration
    }

    environment {
        SONAR_HOST_URL = 'http://localhost:9000'                 // SonarQube URL
        SONAR_TOKEN    = credentials('sonarqube-auth-token')    // Fetch SonarQube token securely
        WORKSPACE_DIR  = "${env.WORKSPACE}"                     // Dynamic workspace path
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/ramky3064/ottbackend-testcase.git',
                    branch: 'main',
                    credentialsId: 'github-PAT'  // GitHub PAT credential
            }
        }

        stage('Build') {
            steps {
                // Use Gradle wrapper; ensure gradlew.bat is committed to repo
                bat "${WORKSPACE_DIR}\\gradlew.bat clean build --no-daemon"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {  // Replace with your SonarQube server name in Jenkins
                    bat """
                    ${WORKSPACE_DIR}\\gradlew.bat sonarqube \
                      -Dsonar.projectKey=ott-backend \
                      -Dsonar.projectName=ott-backend \
                      -Dsonar.host.url=${SONAR_HOST_URL} \
                      -Dsonar.login=${SONAR_TOKEN} \
                      -Dsonar.gradle.skipCompile=true
                    """
                }
            }
        }

        stage('Wait for SonarQube Quality Gate') {
            steps {
                // Pipeline fails if Quality Gate fails
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy with Ansible (WSL)') {
            steps {
                bat """
                wsl -d Ubuntu -- bash -c "ansible-playbook ${WORKSPACE_DIR}/ansible/deploy.yml \
                  -i ${WORKSPACE_DIR}/ansible/inventory.ini \
                  --extra-vars 'workspace=${WORKSPACE_DIR}'"
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline finished. Check console logs for errors."
        }
    }
}
