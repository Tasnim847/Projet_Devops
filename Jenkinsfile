pipeline {
    agent any
    
    stages {
        stage('📥 Git Clone') {
            steps {
                git branch: 'MohamedYoussefMellouli', 
                    url: 'https://github.com/Tasnim847/Projet_Devops.git'
                sh 'echo "✅ Code source récupéré avec succès"'
            }
        }

        stage('🔍 Certification & Vérification') {
            steps {
                echo '🔍 Vérification des outils...'
                sh 'ls -la'
                sh 'mvn --version'
                sh 'docker --version'
                sh 'java -version'
                sh 'echo "✅ Tous les outils sont installés"'
            }
        }

        stage('🏗️ Build Application') {
            steps {
                echo '🏗️ Compilation du projet...'
                sh 'mvn clean compile'
                sh 'echo "✅ Application compilée avec succès"'
            }
        }

        stage('📦 Package Application') {
            steps {
                echo '📦 Création du package JAR...'
                sh 'mvn package -DskipTests'
                
                echo '✅ Vérification des artefacts...'
                sh 'ls -la target/*.jar'
                sh 'echo "✅ JAR créé avec succès"'
            }
        }

        stage('🐳 Docker Container Trace') {
            steps {
                echo '🔍 Trace des conteneurs Docker...'
                sh '''
                    echo "=== LISTE DES CONTENEURS ==="
                    docker ps -a
                    echo ""
                    echo "=== LISTE DES IMAGES ==="
                    docker images
                    echo ""
                    echo "=== STATUT DOCKER ==="
                    docker info | head -10
                    echo "✅ Trace Docker terminée"
                '''
            }
        }
    }
    
    post {
        always {
            echo '📊 Pipeline execution terminée'
        }
        success {
            echo '🎉 SUCCÈS! Pipeline CI complété avec succès!'
            archiveArtifacts 'target/*.jar'
            sh 'echo "📦 JAR archivé - Prêt pour le déploiement futur"'
        }
        failure {
            echo '❌ ÉCHEC du pipeline!'
        }
        cleanup {
            sh 'echo "🧹 Nettoyage terminé"'
        }
    }
}
