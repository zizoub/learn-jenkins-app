pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                ls -la
                node --version
                npm --version
                npm ci 
                npm run build
                ls -la
                '''
            }
        }

        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        # Ensure build output exists
                        test -f build/index.html
                        npm test
                        '''
                    }
                }
                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        echo "Running end-to-end tests..."
                        START_TIME=$(date +%s)
                        
                        # Run Playwright tests
                        npx playwright test
                        
                        END_TIME=$(date +%s)
                        DURATION=$((END_TIME - START_TIME))
                        
                        echo "E2E tests completed in $DURATION seconds"
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npm install netlify-cli -g
                netlify --version
                # Deploy command here, for example:
                # netlify deploy --prod
                '''
            }
        }
    }
}
