pipeline {
    agent any
    
    environment {
        NETLIFY_SITE_ID = '74f96c9d-0523-4721-b0b8-98130ddbc3b2'
        NETLIFY_AUTH_TOKEN = credentials('NETLIFY-TOKEN')
    }

    stages {
        // disabled build stage

        stage('Build'){
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
                    stash name: 'build', includes: 'build/**'
                '''
            }
        }

        stage('Run Tests') {
            parallel {
                stage('Unit Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh "echo 'Beginning Test stage'"
                        sh '''
                            if [ -f build/index.html ]; then
                                echo "index.html exists"
                            else
                                echo "index.html NOT found"
                                exit 1
                            fi
                        '''
                        sh "npm test"
                    }
                }

                stage('Tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-focal'
                            reuseNode true
                        }
                    }

                    steps {
                        sh "echo 'Beginning E2E stage'"
                        sh '''
                            npm install -g serve
                            serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
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
            steps {
                unstash 'build'
                sh '''
                    npm install -g netlify-cli
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    netlify deploy \
                      --dir=build \
                      --prod \
                      --site=$NETLIFY_SITE_ID \
                      --auth=$NETLIFY_AUTH_TOKEN
                '''
            }
        }
    }
     post {
         always {
            junit 'junit-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}

