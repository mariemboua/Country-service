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
                sh 'mvn -B package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQubeServer') {
                    sh '''
                        mvn -B sonar:sonar \
                          -Dsonar.projectKey=country-service \
                          -Dsonar.projectName=country-service
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Build, tests, package et analyse SonarQube termines.'
        }
        failure {
            echo 'Pipeline en echec : consulter Console Output.'
        }
    }
}

