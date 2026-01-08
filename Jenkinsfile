pipeline {
    agent any

    // Ensure tools are defined
    tools {
        jdk 'jdk-21'           // Must match JDK configured in Jenkins Global Tool Configuration
        gradle 'gradle-8.5'    // Use your Gradle installation name
    }

    environment {
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_TOKEN    = 'squ_70491f9364cadd2d1171d8256653ff9a2bcf3bc6'
        WORKSPACE_DIR  = "${env.WORKSPACE}"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/ramky3064/ottbackend-testcase.git',
                    branch: 'main',
                    credentialsId: 'github-PAT'
            }
        }

        stage('Build') {
            steps {
                bat "gradle clean build --no-daemon"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { 
                    bat """
                    gradle sonar \
                      -Dsonar.projectKey=ott-backend \
                      -Dsonar.projectName=ott-backend \
                      -Dsonar.host.url=%SONAR_HOST_URL% \
                      -Dsonar.token=%SONAR_TOKEN% \
                      -Dsonar.gradle.skipCompile=true
                    """
                }
            }
        }

        stage('Deploy with Ansible (WSL)') {
            steps {
                // Safe WSL call; ensures WSL is running and .wslconfig issues are ignored
                bat """
                wsl --shutdown
                wsl -d Ubuntu -- bash -c "ansible-playbook /mnt/c/ProgramData/Jenkins/.jenkins/workspace/ottbackend/ansible/deploy.yml \
                  -i /mnt/c/ProgramData/Jenkins/.jenkins/workspace/ottbackend/ansible/inventory.ini \
                  --extra-vars 'workspace=/mnt/c/ProgramData/Jenkins/.jenkins/workspace/ottbackend'"
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
