pipeline {
    agent any

    enviroment {
        NETLIFY_SITE_ID = '117377bc-cf00-4cb9-ab48-eb00b9e44011'
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
        stage('Test'){
            parallel{
                stage('Unit Tests'){
                    agent{
                        docker{
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                steps{
                    sh '''
                    test -f build/index.html
                    npm test
                    '''
                }
                post{
                    always{
                        junit 'jest-results/junit.xml'
                    }
                }
            }
        stage('E2E'){
            agent{
                docker{
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }

            steps{
                sh '''
                npm install serve
                node_modules/.bin/serve -s build &
                sleep 10
                npx playwright test --reporter=html
                '''
            }
            post{
                always{
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'LEARN-JENKINS-APP/build/reports/codenarc'])
                }
            }
        }
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
                '''
            }
        }
    }
}
