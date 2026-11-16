pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven'
    }

    stages {
        stage('Checkout code') {
            steps {
                checkout scm
            }
        }

        stage('Compile code') {
            steps {
                sh 'mvn -B clean compile'
            }
        }

        stage('Test code') {
            steps {
                sh 'mvn -B test'
            }
            post {
                always {
                    junit allowEmptyResults: true,
                          testResults: '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }

        stage('Package code') {
            steps {
                sh 'mvn -B package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQubeServer') {
                    sh '''
                    mvn -B org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                    -Dsonar.projectKey=country-service \
                    -Dsonar.projectName=country-service
                    '''
                }
            }
        }

        stage('Build, push & deploy (Ansible)') {
            steps {
                withCredentials([string(credentialsId: 'ansible-vault-pass', variable: 'VAULT_PASS')]) {
                    sh '''
                    trap 'rm -f .vault_pass.tmp' EXIT
                    echo "$VAULT_PASS" > .vault_pass.tmp
                    chmod 600 .vault_pass.tmp
                    ansible-playbook playbookCICD.yml \
                      --vault-password-file .vault_pass.tmp \
                      --extra-vars "image_tag=$BUILD_NUMBER"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline complet termine : build, tests, SonarQube, Docker, deploiement.'
        }
        failure {
            echo 'Pipeline en echec : consulter Console Output.'
        }
    }
}

