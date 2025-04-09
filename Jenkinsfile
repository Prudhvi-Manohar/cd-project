pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image with BUILD_ID for versioning
                    bat 'docker build -t flask-app:%BUILD_ID% .'
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    // Clean up any existing test container first - using PowerShell for better error handling
                    bat '''
                        powershell -Command "if (docker ps -a --format '{{.Names}}' | Select-String -Pattern '^flask-test$') { docker stop flask-test; docker rm flask-test } else { Write-Host 'No existing flask-test container found.' }"
                    '''
                    
                    // Start the container for testing
                    bat 'docker run -d --name flask-test -p 5001:5000 flask-app:%BUILD_ID%'
                    
                    // Wait for app to start - using ping as a delay technique in Windows
                    bat 'ping -n 6 127.0.0.1 > nul'
                    
                    // Verify the application is running by making a request
                    bat '''
                        powershell -command "try { $response = Invoke-WebRequest -Uri http://localhost:5001 -UseBasicParsing; if($response.StatusCode -eq 200) { Write-Host 'Test successful!'; exit 0 } else { Write-Host 'Test failed!'; exit 1 } } catch { Write-Host 'Failed to connect to test application!'; exit 1 }"
                    '''
                }
            }
            post {
                always {
                    // Clean up test container whether the test passed or failed - using PowerShell for better error handling
                    bat '''
                        powershell -Command "if (docker ps -a --format '{{.Names}}' | Select-String -Pattern '^flask-test$') { docker stop flask-test; docker rm flask-test; Write-Host 'Test container cleaned up.' } else { Write-Host 'No flask-test container to clean up.' }"
                        exit 0
                    '''
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    // Clean up any existing production container - using PowerShell for better error handling
                    bat '''
                        powershell -Command "if (docker ps -a --format '{{.Names}}' | Select-String -Pattern '^flask-production$') { docker stop flask-production; docker rm flask-production; Write-Host 'Removed old production container.' } else { Write-Host 'No existing production container found.' }"
                    '''
                    
                    // Deploy new version
                    bat 'docker run -d --name flask-production -p 5000:5000 flask-app:%BUILD_ID%'
                    
                    // Verify deployment
                    bat 'ping -n 6 127.0.0.1 > nul'
                    bat '''
                        powershell -command "try { $response = Invoke-WebRequest -Uri http://localhost:5000 -UseBasicParsing; if($response.StatusCode -eq 200) { Write-Host 'Deployment verified successfully!' } else { Write-Host 'Deployment verification failed!'; exit 1 } } catch { Write-Host 'Failed to connect to production application!'; exit 1 }"
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
            
            // Additional cleanup in case of failure - using PowerShell for better error handling
            bat '''
                powershell -Command "if (docker ps -a --format '{{.Names}}' | Select-String -Pattern '^flask-test$') { docker stop flask-test; docker rm flask-test; Write-Host 'Cleaned up test container after failure.' }"
                exit 0
            '''
        }
    }
}
