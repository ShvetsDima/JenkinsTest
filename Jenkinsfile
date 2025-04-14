pipeline {
    agent any
    
    environment {
        SUDO_PASSWORD = 'jenkins'
    }
    
    stages {
        stage('Install Apache2') {
            steps {
                sh '''
                    if command -v apt-get >/dev/null 2>&1; then
                        echo "$SUDO_PASSWORD" | sudo -S apt-get update && sudo -S apt-get install -y apache2
                    elif command -v yum >/dev/null 2>&1; then
                        echo "$SUDO_PASSWORD" | sudo -S yum update -y && sudo -S yum install -y httpd
                    else
                        echo "Unsupported package manager"
                        exit 1
                    fi
                '''
            }
        }
        
        stage('Start Apache2 Service') {
            steps {
                sh '''
                    if command -v systemctl >/dev/null 2>&1; then
                        echo "$SUDO_PASSWORD" | sudo -S systemctl start apache2 || sudo -S systemctl start httpd
                        echo "$SUDO_PASSWORD" | sudo -S systemctl enable apache2 || sudo -S systemctl enable httpd
                    else
                        echo "$SUDO_PASSWORD" | sudo -S service apache2 start || sudo -S service httpd start
                    fi
                '''
            }
        }
        
        stage('Check Apache2 Logs') {
            steps {
                sh '''
                    # Determine log file location based on OS
                    if [ -f /var/log/apache2/access.log ]; then
                        LOG_FILE="/var/log/apache2/access.log"
                    elif [ -f /var/log/httpd/access_log ]; then
                        LOG_FILE="/var/log/httpd/access_log"
                    else
                        echo "Log file not found"
                        exit 1
                    fi
                    
                    # Check for 4xx and 5xx errors
                    echo "Checking for 4xx and 5xx errors in ${LOG_FILE}"
                    grep -E "HTTP/[0-9.]+ [45][0-9]{2}" ${LOG_FILE} || echo "No 4xx/5xx errors found"
                '''
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
