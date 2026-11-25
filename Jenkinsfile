pipeline {
    agent any

    environment {
        NEXUS_URL   = 'http://localhost:8081/repository/python-release/Release.zip'
        NEXUS_CREDS = credentials('1')   
        BASE_DIR    = 'C:\\Users\\lenov\\Documents\\Development\\ILIA3\\SoftwareEngineering\\serveurs'
    }

    stages {
        stage('Nettoyage du Workspace') {
            steps { deleteDir() }
        }

        stage('Téléchargement de la Release') {
            steps {
                bat '''
                    curl --fail -L -u %NEXUS_CREDS_USR%:%NEXUS_CREDS_PSW% ^
                    -o Release.zip ^
                    %NEXUS_URL%
                '''
            }
        }

        stage('Vérification du téléchargement') {
            steps {
                bat '''
                    if not exist Release.zip (
                        echo ERREUR : Release.zip introuvable
                        exit /b 1
                    )
                    dir
                '''
            }
        }

        stage('Décompression') {
            steps {
                bat 'powershell -Command "Expand-Archive -Path Release.zip -DestinationPath Release -Force"'
            }
        }

        stage('Déploiement DEV') {
            steps {
                bat '''
                    if not exist "%BASE_DIR%\\DEV" mkdir "%BASE_DIR%\\DEV"
                    xcopy Release "%BASE_DIR%\\DEV" /E /I /Y
                '''
            }
        }

        stage('Déploiement TEST') {
            steps {
                bat '''
                    if not exist "%BASE_DIR%\\TEST" mkdir "%BASE_DIR%\\TEST"
                    xcopy Release "%BASE_DIR%\\TEST" /E /I /Y
                '''
            }
        }

        stage('Déploiement UAT') {
            steps {
                bat '''
                    if not exist "%BASE_DIR%\\UAT" mkdir "%BASE_DIR%\\UAT"
                    xcopy Release "%BASE_DIR%\\UAT" /E /I /Y
                '''
            }
        }

        stage('Déploiement STAGING') {
            steps {
                bat '''
                    if not exist "%BASE_DIR%\\STAGING" mkdir "%BASE_DIR%\\STAGING"
                    xcopy Release "%BASE_DIR%\\STAGING" /E /I /Y
                '''
            }
        }

        stage('Déploiement PRODUCTION') {
            steps {
                bat '''
                    if not exist "%BASE_DIR%\\PRODUCTION" mkdir "%BASE_DIR%\\PRODUCTION"
                    xcopy Release "%BASE_DIR%\\PRODUCTION" /E /I /Y
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'Release.zip', allowEmptyArchive: true, fingerprint: true
        }
        success { echo 'Déploiement terminé avec succès.' }
        failure { echo 'Le pipeline a échoué. Vérifier les logs Jenkins.' }
    }
}