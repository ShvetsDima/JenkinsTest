pipeline {
    agent any
    stages {
        stage('Install Apache') {
            steps {
                sshagent (credentials: ['remote-vm-ssh']) {
                    sh 'ssh -o StrictHostKeyChecking=no user@remote-ip "sudo apt update && sudo apt install apache2 -y"'
                }
            }
        }
        stage('Check Logs') {
            steps {
                sshagent (credentials: ['remote-vm-ssh']) {
                    sh '''
                        ssh user@remote-ip "
                            cat /var/log/apache2/access.log | \
                            grep -E 'HTTP/1.1\" [45][0-9][0-9]' || echo 'No 4xx/5xx errors found.'
                        "
                    '''
                }
            }
        }
    }
}
