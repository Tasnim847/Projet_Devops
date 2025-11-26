pipeline {
    agent any

    tools {
        maven "M2_HOME"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Tasnim847/Projet_Devops.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        
    }

    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
    }
}

