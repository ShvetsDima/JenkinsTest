pipeline {
    agent any
    environment {
        REMOTE_USER = 'kali'
        REMOTE_HOST = '172.16.10.135'
    }
    stages {
        stage('Install Apache') {
            steps {
                sshagent (credentials: ['remote-vm-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST \
                        "sudo apt update && sudo apt install apache2 -y"
                    '''
                }
            }
        }
        stage('Check Logs') {
            steps {
                sshagent (credentials: ['remote-vm-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST "
                            cat /var/log/apache2/access.log | \
                            grep -E 'HTTP/1.1\\\" [45][0-9][0-9]' || echo 'No 4xx/5xx errors found.'
                        "
                    '''
                }
            }
        }
    }
}
