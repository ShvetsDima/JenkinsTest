pipeline {
    agent any
    
    stages {
        stage('Install Apache2') {
            steps {
                sh '''
                    if command -v apt-get >/dev/null 2>&1; then
                        sudo apt-get update && sudo apt-get install -y apache2
                    elif command -v yum >/dev/null 2>&1; then
                        sudo yum update -y && sudo yum install -y httpd
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
                        sudo systemctl start apache2 || sudo systemctl start httpd
                        sudo systemctl enable apache2 || sudo systemctl enable httpd
                    else
                        sudo service apache2 start || sudo service httpd start
                    fi
                '''
            }
        }
        
        stage('Check Apache2 Logs') {
            steps {
                sh '''
                    # Search for the access log file using locate command
                    ACCESS_LOG=$(sudo locate access.log | grep -E "/apache2/|/httpd/" | head -n 1)
                    
                    if [ -z "$ACCESS_LOG" ]; then
                        # If access.log is not found, search for access_log
                        ACCESS_LOG=$(sudo locate access_log | grep -E "/apache2/|/httpd/" | head -n 1)
                    fi
                    
                    if [ -z "$ACCESS_LOG" ]; then
                        echo "Access log file not found"
                        exit 1
                    fi
                    
                    # Check for 4xx and 5xx errors
                    echo "Checking for 4xx and 5xx errors in ${ACCESS_LOG}"
                    sudo grep -E "HTTP/[0-9.]+ [45][0-9]{2}" ${ACCESS_LOG} || echo "No 4xx/5xx errors found"
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
