pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'c9dce1a5-8696-4fb6-919b-9a276bc87d5e'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        NETLIFY_SKIP_BUILD = 'true'
        NETLIFY_BUILD_COMMAND = ''
    }

    stages {
        stage('Build') {
            agent {
                docker{
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

        stage('Test'){
            agent {
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            
            steps {
                    sh '''
                        test -f build/index.html
                        CI=true npm test -- --watchAll=false

                        '''
                }
            }
        stage('Deploy') {
            agent {
                docker{
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli 
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID : $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod --build=false --message "Jenkins deploy"
                '''
            }
        }
    }

    post{
        always{
            junit 'test-results/junit.xml'
        }
    }
}
