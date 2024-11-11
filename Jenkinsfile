pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '117377bc-cf00-4cb9-ab48-eb00b9e44011'
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
        stage('Deploy'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                    npm install netlify-cli -g
                    node_modules/.bin/netlify --version
                    echo "Deploying to prod. Site id: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}
