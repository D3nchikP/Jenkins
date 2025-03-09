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
                    sh '''
                    docker run --rm bridgecrew/checkov:latest \
                    --repo-id DenisPrisma/DEMOREPO \
                    --repo-root-for-plan-enrichment https://github.com/D3nchikP/DEMOREPO.git \
                    --branch main \
                    --use-enforcement-rules -o cli \
                    --prisma-api-url ${PRISMA_API_URL} \
                    --bc-api-key ${ACCESS_KEY}::${SECRET_KEY}
                    '''
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
