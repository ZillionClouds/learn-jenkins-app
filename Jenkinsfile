pipeline{
    agent any

    environment {
        NETLIFY_SITE_ID = "ce1c5692-c35d-4cb0-ab44-fcb9ca086251"
        NETLIFY_AUTH_TOKEN = credentials("netlify-token")
    }

    stages{
        stage("Docker"){
            steps{
                sh 'docker build -t my-jenkins .'
            }
        }
        stage("Build"){
            agent {
                docker {
                    image 'my-jenkins'
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

        // stage("Run Parallel Tests"){
        //     parallel{
        //         stage("Test"){
        //             agent {
        //                 docker {
        //                     image 'node:18-alpine'
        //                     reuseNode true
        //                 }
        //             }
        //             steps {
        //                 sh '''
        //                     test -f build/index.html
        //                     npm test
        //                 '''
        //             }
        //         }
        //         stage("Test 2"){
        //             agent {
        //                 docker {
        //                     image 'node:18-alpine'
        //                     reuseNode true
        //                 }
        //             }
        //             steps {
        //                 sh '''
        //                     npm --version
        //                     node --version
        //                 '''
        //             }
        //         }
        //     }
        // }

        stage("Deploy"){
            agent {
                docker {
                    image 'my-jenkins'
                    reuseNode true
                }
            }

            steps{
                sh '''
                    echo "Netlify CLI version:"
                    netlify --version

                    echo "Checking Netlify site status..."
                    netlify status || {
                        echo "Netlify status check failed"
                        exit 1
                    }

                    echo "Deploying to Netlify..."
                    netlify deploy --dir=build --prod
                '''
            }
        }
    }
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}