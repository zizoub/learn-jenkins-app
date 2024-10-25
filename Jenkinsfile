pipeline {
    agent any
    environment{
        NETLIFY_SITE_ID = 'f51887f6-246f-43c4-8fbd-e0cb89479863'
        NETLIFY_AUTH_TOKEN = credentials('netlify_token') 
    }
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
                npm install netlify-cli 
                node_modules/.bin/netlify --version
                echo " deploying to production. site ID : $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status

                # Deploy command here, for example:
                node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}
