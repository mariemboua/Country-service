pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Code source recupere depuis GitHub'
            }
        }

        stage('Compilation') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }
    }

    post {
        success {
            echo 'Pipeline termine avec succes !'
        }
        failure {
            echo 'Le pipeline a echoue. Consulter la console Jenkins.'
        }
    }
}
