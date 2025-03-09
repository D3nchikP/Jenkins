pipeline {
    agent any

    environment {
         PRISMA_API_URL = 'https://api2.prismacloud.io'
         ACCESS_KEY     = credentials('PRISMA_ACCESS_KEY')
         SECRET_KEY     = credentials('PRISMA_SECRET_KEY')
    }

    stages {
        stage ('Checkov') {
            steps {
                script {
                    docker.image('bridgecrew/checkov:latest').inside("--entrypoint='' -u root") {
                        sh '''
                            checkov -d . \
                                --use-enforcement-rules \
                                --prisma-api-url ${PRISMA_API_URL} \
                                --bc-api-key ${ACCESS_KEY}::${SECRET_KEY} \
                                --repo-id D3nchikP/terragoat \
                                --branch main \
                                -o cli -o junitxml \
                                --output-file-path console,results.xml
                        '''
                    }
                } 
            }
            post {
                always {
                    junit 'results.xml'
                }
            }
        }

        stage ('Deploy') {
            steps {
                script {
                    sh "echo 'deploy'"
                }
            }
        }
    }
}
