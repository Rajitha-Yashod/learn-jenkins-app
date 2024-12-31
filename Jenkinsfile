pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '9a45529e-f57a-476c-8030-ef19087a0e57'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {


        stage('Docker') {
            steps {
                sh 'docker build -t my-image .'
            }
        }

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

        stage('Appoval'){
            steps{
                timeout(time: 1, unit: 'HOURS') {
                    input message: 'Deploy to production', ok: 'Approve'
                }   
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
                netlify --version
                echo "Deploying to Netlify site Id: $NETLIFY_SITE_ID"
                netlify status
                netlify deploy --dir=build --prod
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
