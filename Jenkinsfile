pipeline {
    agent any

    environment {
        PRISMA_API_URL = 'https://api.prismacloud.io'
        ACCESS_KEY = credentials('pc-access-key')
        SECRET_KEY = credentials('pc-secret-key')
    }

    stages {
        stage('Checkout Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/D3nchikP/Jenkins.git'
            }
        }

        stage('Checkov Scan') {
            steps {
                script {
                    withDockerContainer(image: 'bridgecrew/checkov:latest') {
                        sh '''
                        checkov -d . --use-enforcement-rules \
                        -o cli -o junitxml --output-file-path console,${WORKSPACE}/results.xml \
                        --prisma-api-url ${PRISMA_API_URL} --bc-api-key ${ACCESS_KEY}::${SECRET_KEY} --repo-id D3nchikP/Jenkins --branch main
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            junit skipPublishingChecks: true, testResults: '${WORKSPACE}/results.xml'
        }
    }
}
