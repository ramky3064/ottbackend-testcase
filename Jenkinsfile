pipeline {
    agent any

    tools {
        jdk 'jdk-21'
        gradle 'Gradle'
    }

    environment {
        SONAR_PROJECT_KEY  = 'ott-backend'
        SONAR_PROJECT_NAME = 'ott-backend'
        ANSIBLE_PLAYBOOK   = 'ansible/deploy.yml'
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
                      -Dsonar.token=squ_70491f9364cadd2d1171d8256653ff9a2bcf3bc6\
                      -Dsonar.gradle.skipCompile=true
                    '''
                }
            }
        }

stage('Deploy with Ansible') {
    steps {
        bat '''
        echo Running Ansible Deployment...
        wsl ansible-playbook %ANSIBLE_PLAYBOOK% -i ansible/inventory.ini ^
        --extra-vars "workspace=%WORKSPACE%"
        '''
    }
}
    }
}
