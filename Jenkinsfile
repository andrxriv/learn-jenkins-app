pipeline {
    agent any
    
    stages {
        // stage('Init'){
        //     steps {
        //         sh 'docker exec -u root -it jenkins bash -c "apt-get update && apt-get install -y nodejs npm"'
        //     }
        // }
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
                    // ls -la
                '''
            }
        }

        stage('Test') {
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
}

