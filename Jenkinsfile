pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/lexca212/devops1.git'
            }
        }

        stage('Deploy to STAGING') {
            steps {
                sshagent(['ssh-vps']) {
            sh '''
            ssh -o StrictHostKeyChecking=no lexca@192.168.88.99 "
                cd /var/www/html/devops1 || exit
                git pull origin master
            "
            '''
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
                    ssh -o StrictHostKeyChecking=no lexca@192.168.88.72 '
                        cd /var/www/html/devops1 || exit
                        git pull origin master
                    '
                    """
                }
            }
        }
    }
}
