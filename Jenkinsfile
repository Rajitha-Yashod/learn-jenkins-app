pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '9a45529e-f57a-476c-8030-ef19087a0e57'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh'''
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
        }
         stage('deploy'){ 
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh'''
                echo "Done small changes"
                npm install netlify-cli 
                node_modules/.bin/netlify --version
                echo "Deploying to Netlify site Id: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --prod
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
