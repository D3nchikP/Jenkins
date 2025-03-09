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
                                --repo-id DenisPrisma/DEMOREPO \
                                --branch main \
                                -o cli -o junitxml \
                                --output-file-path console,results.xml || true
                        '''
                    }
                } 
            }
            post {
                always {
                    script {
                        if (fileExists('results.xml')) {
                            junit 'results.xml'
                        } else {
                            echo "No Checkov results found; skipping JUnit reporting."
                        }
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
