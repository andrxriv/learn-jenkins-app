pipeline {
    agent any
    
    stages {
        // disabled build stage

        // stage('Build'){
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             ls -la
        //             node --version
        //             npm --version
        //             npm ci
        //             npm run build
        //         '''
        //     }
        // }

        stage('Test') {
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

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.57.0-noble'
                    reuseNode true
                }
            }

            steps {
                sh "echo 'Beginning E2E stage'"
                sh '''
                npm install -g serve
                serve -s build &
                sleep 10
                npx playwright test
                '''
            }
        }
    }
     post {
        always {
            junit 'junit-results/junit.xml'
        }
    }
}

