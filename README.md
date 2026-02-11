tutorial setup ci cd dengan jenkins
1. masuk ke server jenkins ( ssh jenkins@IP_ADDR)
2. generate ssh keygen ( ssh-keygen -t rsa -b 4096 -C "jenkins@cicd" )
3. terus copy ssh ssh-copy-id user@IP_STAGING
ssh-copy-id user@IP_PRODUCTION
4. Manage Jenkins → Credentials → Global → Add
Type:
Username + Password (atau GitHub Token)
ID : github-creds

5. SSH Credential ke VPS

Add Credential:

Type: SSH Username with private key

Username: user

Private Key: isi dari /root/.ssh/id_rsa
id : ssh-vps

6.2 Pipeline dari SCM

Definition: Pipeline script from SCM

SCM: Git

Repo URL: https://github.com/USERNAME/sisur-web.git

Credential: github-creds

Branch: */main

Script Path: Jenkinsfile
7. isi jenkins file :
===========
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

8. di vps staging 
sudo mkdir -p /var/www/sisur
sudo chown -R user:user /var/www/sisur
git clone https://github.com/USERNAME/sisur-web.git /var/www/sisur

di VPS Prod
sudo mkdir -p /var/www/sisur
sudo chown -R user:user /var/www/sisur
git clone https://github.com/USERNAME/sisur-web.git /var/www/sisur


jelasnya disini 

https://chatgpt.com/g/g-p-68f5eb0afd148191ae4f90bf5f1e9081-devops-cicd/c/69782dd9-80d8-8320-9a2c-003b7eb5fd64
