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
                    docker run --rm --user root \
                    -v /tmp/bridgecrew:/root/.bridgecrew \
                    -v "/var/lib/jenkins/workspace/Checkov Scan:/tf" \
                    bridgecrew/checkov:latest \
                    -d /tf --use-enforcement-rules -o cli \
                    --prisma-api-url ${PRISMA_API_URL} \
                    --bc-api-key ${ACCESS_KEY}::${SECRET_KEY} \
                    --repo-id DenisPrisma/DEMOREPO --branch main
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
