pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/USERNAME/sisur-web.git'
            }
        }

        stage('Deploy to STAGING') {
            steps {
                sshagent(['ssh-vps']) {
                    sh """
                    ssh user@IP_STAGING '
                        cd /var/www/sisur || exit
                        git pull origin main
                    '
                    """
                }
            }
        }

        stage('Approval to Production') {
            steps {
                input message: 'Deploy ke PRODUCTION?', ok: 'Deploy'
            }
        }

        stage('Deploy to PRODUCTION') {
            steps {
                sshagent(['ssh-vps']) {
                    sh """
                    ssh user@IP_PRODUCTION '
                        cd /var/www/sisur || exit
                        git pull origin main
                    '
                    """
                }
            }
        }
    }
}
