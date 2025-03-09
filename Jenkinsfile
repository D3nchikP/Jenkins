pipeline {

    agent any

    environment {
         PRISMA_API_URL =   'https://api2.prismacloud.io'
         ACCESS_KEY     =   credentials('PRISMA_ACCESS_KEY')
         SECRET_KEY     =   credentials('PRISMA_SECRET_KEY')
    }

    stages {
        stage ('Checkov') {
            steps {
                script {
                    docker.image('bridgecrew/checkov:latest').inside("--entrypoint=''") {
                        sh '''
                        checkov -d . --use-enforcement-rules -o cli -o junitxml \
                        --output-file-path console,results.xml \
                        --prisma-api-url ${PRISMA_API_URL} \
                        --bc-api-key ${ACCESS_KEY}::${SECRET_KEY} \
                        --repo-id DenisPrisma/DEMOREPO --branch main
                        '''
                    }
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
