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

        stage('🧪 Tests avec Base de Données') {
            steps {
                echo '🧪 Démarrage de MySQL pour les tests...'
                sh '''
                    docker run -d \
                        --name test-mysql \
                        -e MYSQL_ROOT_PASSWORD=root \
                        -e MYSQL_DATABASE=student_db \
                        -e MYSQL_USER=testuser \
                        -e MYSQL_PASSWORD=testpass \
                        -p 3306:3306 \
                        mysql:8.0
                    
                    echo "⏳ Attente du démarrage de MySQL..."
                    sleep 30
                    
                    docker exec test-mysql mysqladmin ping -h localhost -u root -proot || sleep 10
                    
                    echo "✅ MySQL démarré"
                '''

                echo '🧪 Exécution des tests unitaires...'
                sh '''
                    mvn test \
                        -Dspring.datasource.url=jdbc:mysql://localhost:3306/student_db \
                        -Dspring.datasource.username=root \
                        -Dspring.datasource.password=root
                '''
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    sh 'echo "📊 Rapports de tests générés"'
                    sh '''
                        echo "🧹 Suppression conteneur MySQL..."
                        docker stop test-mysql || true
                        docker rm test-mysql || true
                    '''
                }
            }
        }

        stage('🐳 Build Docker Image') {
            steps {
                echo '🐳 Construction de l’image Docker...'
                sh '''
                    docker build -t tasnim847/student-app:1.0 .
                    echo "✅ Image Docker créée"
                    docker images | grep student-app
                '''
            }
        }

        stage('🔍 Analyse SonarQube') {
            steps {
                withCredentials([string(credentialsId: 'jenkins_sonar', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        echo "🔍 Analyse SonarQube"
                        mvn sonar:sonar -Dsonar.projectKey=Devops \
                                        -Dsonar.host.url=http://192.168.217.135:9000 \
                                        -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }

        stage('📦 Package Application (JAR Final)') {
            steps {
                echo '📦 Packaging du projet...'
                sh 'mvn package -DskipTests'
                
                echo '📄 Vérification du JAR...'
                sh 'ls -la target/*.jar'
                sh 'echo "🎉 JAR final créé avec succès"'
            }
        }
    }
    
    post {
        always {
            echo '📊 Pipeline execution terminée'
            sh '''
                echo "🧹 Nettoyage final..."
                docker stop test-mysql || true
                docker rm test-mysql || true
            '''
        }
        success {
            echo '🎉 SUCCÈS! Pipeline CI complété avec succès!'
            archiveArtifacts 'target/*.jar'
        }
        failure {
            echo '❌ ÉCHEC du pipeline!'
        }
        cleanup {
            sh 'echo "🧹 Nettoyage terminé"'
        }
    }
}

