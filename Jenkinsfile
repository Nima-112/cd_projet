pipeline {
    agent any
    
    environment {
        NEXUS_URL = 'http://localhost:8081/repository/python-release/Release.zip'
        NEXUS_USER = 'admin'
        NEXUS_PASSWORD = credentials('nexus-admin-pass')
        
        // Votre chemin de base
        BASE_DIR = 'C:\\Users\\lenov\\Documents\\Development\\ILIA3\\SoftwareEngineering\\serveurs'
    }
    
    stages {
        stage('Nettoyage du Workspace') {
            steps {
                deleteDir()
            }
        }
        
        stage('Téléchargement de la Release') {
            steps {
                bat """
                    curl -u %NEXUS_USER%:%NEXUS_PASSWORD% ^
                    -o Release.zip ^
                    %NEXUS_URL%
                """
            }
        }
        
        stage('Vérification du téléchargement') {
            steps {
                bat """
                    if not exist Release.zip (
                        echo ERREUR : Release.zip introuvable
                        exit /b 1
                    )
                    dir
                """
            }
        }
        
        stage('Décompression de la Release') {
            steps {
                bat """
                    powershell -Command "Expand-Archive -Path Release.zip -DestinationPath Release -Force"
                """
            }
        }
        
        stage('Vérification de la Release') {
            steps {
                bat """
                    if not exist Release (
                        echo ERREUR : dossier Release introuvable
                        exit /b 1
                    )
                    dir Release
                """
            }
        }
        
        stage('Déploiement DEV') {
            steps {
                bat """
                    if not exist "%BASE_DIR%\\DEV" mkdir "%BASE_DIR%\\DEV"
                    xcopy Release "%BASE_DIR%\\DEV" /E /I /Y
                """
            }
        }
        
        stage('Vérification DEV') {
            steps {
                bat """
                    echo ======= CONTENU DEV =======
                    dir "%BASE_DIR%\\DEV"
                    if exist "%BASE_DIR%\\DEV\\calculator.py" (
                        python "%BASE_DIR%\\DEV\\calculator.py"
                    )
                """
            }
        }
        
        stage('Déploiement TEST') {
            steps {
                bat """
                    if not exist "%BASE_DIR%\\TEST" mkdir "%BASE_DIR%\\TEST"
                    xcopy Release "%BASE_DIR%\\TEST" /E /I /Y
                """
            }
        }
        
        stage('Tests fonctionnels') {
            steps {
                bat """
                    if exist "%BASE_DIR%\\TEST\\test_calculator.py" (
                        python "%BASE_DIR%\\TEST\\test_calculator.py"
                    ) else (
                        echo Aucun test fonctionnel trouve.
                    )
                """
            }
        }
        
        stage('Déploiement UAT') {
            steps {
                bat """
                    if not exist "%BASE_DIR%\\UAT" mkdir "%BASE_DIR%\\UAT"
                    xcopy Release "%BASE_DIR%\\UAT" /E /I /Y
                """
            }
        }
        
        stage('Déploiement STAGING') {
            steps {
                bat """
                    if not exist "%BASE_DIR%\\STAGING" mkdir "%BASE_DIR%\\STAGING"
                    xcopy Release "%BASE_DIR%\\STAGING" /E /I /Y
                """
            }
        }
        
        stage('Déploiement PRODUCTION') {
            steps {
                bat """
                    if not exist "%BASE_DIR%\\PRODUCTION" mkdir "%BASE_DIR%\\PRODUCTION"
                    xcopy Release "%BASE_DIR%\\PRODUCTION" /E /I /Y
                """
            }
        }
    }
    
    post {
        success {
            echo '========================================='
            echo 'Déploiement terminé avec succès.'
            echo '========================================='
        }
        failure {
            echo '========================================='
            echo 'Le pipeline a échoué.'
            echo 'Vérifier les logs Jenkins.'
            echo '========================================='
        }
        always {
            archiveArtifacts artifacts: 'Release.zip', fingerprint: true
        }
    }
}