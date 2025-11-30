pipeline {
    agent any
    
    stages {

        stage('📥 Git Clone') {
            steps {
                git branch: 'Tasnim',
                    url: 'https://github.com/Tasnim847/Projet_Devops.git'
                
                sh 'echo "✅ Code source récupéré avec succès"'
            }
        }

        stage('🔍 Vérification des outils') {
            steps {
                echo '🔍 Vérification...'
                sh 'mvn --version'
                sh 'java -version'
                sh 'echo "✅ Outils OK"'
            }
        }

        stage('🏗️ Build') {
            steps {
                echo '🏗️ Compilation...'
                sh 'mvn clean compile'
                sh 'echo "✅ Build réussi"'
            }
        }

        stage('🧪 Tests') {
            steps {
                echo '🧪 Exécution des tests...'
                sh 'mvn test'
                sh 'echo "✅ Tests terminés avec succès"'
            }
        }

        stage('📦 Package (.jar)') {
            steps {
                echo '📦 Création du JAR...'
                sh 'mvn package -DskipTests'

                echo '📁 Vérification des artefacts...'
                sh 'ls -la target/*.jar'

                sh 'echo "🎉 JAR créé avec succès !"'
            }
        }
    }

    post {
        always {
            echo '📊 Fin du pipeline'
        }
        success {
            echo '🎉 Pipeline terminé avec succès !'
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
        failure {
            echo '❌ Le pipeline a échoué'
        }
        cleanup {
            sh 'echo "🧹 Nettoyage terminé"'
        }
    }
}

