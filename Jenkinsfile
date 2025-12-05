pipeline {
    agent any
    
    stages {
        stage('📥 1.  Git Clone') {
            steps {
                git branch: 'Tasnim', 
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

        stage('🏗️  2. Build Application') {
            steps {
                echo '🏗️ Compilation du projet...'
                sh 'mvn clean compile'
                sh 'echo "✅ Application compilée avec succès"'
            }
        }

        stage('🧪 3. Tests avec Base de Données') {
            steps {
                echo '🧪 Démarrage de MySQL pour les tests...'
                sh '''
                    # Démarrer un conteneur MySQL
                    docker run -d \\
                        --name test-mysql \\
                        -e MYSQL_ROOT_PASSWORD=root \\
                        -e MYSQL_DATABASE=student_db \\
                        -e MYSQL_USER=testuser \\
                        -e MYSQL_PASSWORD=testpass \\
                        -p 3306:3306 \\
                        mysql:8.0
                    
                    echo "⏳ Attente du démarrage de MySQL..."
                    sleep 30
                    
                    # Vérifier que MySQL est opérationnel
                    docker exec test-mysql mysqladmin ping -h localhost -u root -proot || sleep 10
                    
                    echo "✅ MySQL démarré avec succès"
                    echo "📊 Base de données créée: student_db"
                '''
                
                echo '🧪 Exécution des tests avec base de données...'
                sh '''
                    mvn test \\
                        -Dspring.datasource.url=jdbc:mysql://localhost:3306/student_db \\
                        -Dspring.datasource.username=root \\
                        -Dspring.datasource.password=root \\
                        -Dspring.jpa.hibernate.ddl-auto=update \\
                        -Dspring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
                '''
                
                sh 'echo "✅ Tests exécutés avec succès avec base de données"'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    sh 'echo "📊 Rapports de tests générés"'
                    
                    // Nettoyage de la base de données de test
                    sh '''
                        echo "🧹 Nettoyage du conteneur MySQL..."
                        docker stop test-mysql || true
                        docker rm test-mysql || true
                        echo "✅ Base de données de test nettoyée"
                    '''
                }
            }
        }

        stage('📦 4. Package Application') {
            steps {
                echo '📦 Création du package JAR...'
                sh 'mvn package -DskipTests'
                
                echo '✅ Vérification des artefacts...'
                sh 'ls -la target/*.jar'
                sh 'echo "✅ JAR créé avec succès"'
            }
        }
        
        stage('🔍 5. SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        echo "🔍 Analyse SonarQube"
                        mvn sonar:sonar -Dsonar.projectKey=Devops \
                                        -Dsonar.host.url=http://192.168.217.135:9000 \
                                        -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }
    }
    
    post {
        always {
            echo '📊 Pipeline execution terminée'
            
            // Nettoyage garantie même en cas d'échec
            sh '''
                echo "🧹 Nettoyage final des conteneurs..."
                docker stop test-mysql || true
                docker rm test-mysql || true
            '''
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
