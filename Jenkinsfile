pipeline {
    agent any
    environment {
        NETLIFY_SITE_ID = 'f0a32e4c-6d48-4436-baa9-85eb9f79d4c8'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
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

        stage('Unit tests') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    mkdir -p jest-results
                    export JEST_JUNIT_OUTPUT_DIR=jest-results
                    export JEST_JUNIT_OUTPUT_NAME=junit.xml
                    npm test
                '''
            }
            post {
                always {
                    junit 'jest-results/junit.xml'
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
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                '''
            }
        }
    }
}