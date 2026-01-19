pipeline {
    agent any
    
    stages {
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
    }
     post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}

