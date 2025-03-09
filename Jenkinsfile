pipeline {
    agent any

    environment {
        PRISMA_API_URL = credentials('PRISMA_CLOUD_API')
        ACCESS_KEY = credentials('PRISMA_ACCESS_KEY')
        SECRET_KEY = credentials('PRISMA_SECRET_KEY')
    }

    stages {
        stage('Checkout Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/DenisPrisma/DEMOREPO.git'
            }
        }

        stage('Checkov Scan') {
            steps {
                script {
                    sh '''
                    # Ensure we're using Bash
                    export WORKSPACE_ESCAPED="$(echo $WORKSPACE | sed 's/ /\\\\ /g')"

                    # Run Checkov inside Docker (No XML, Prisma URL only)
                    docker run --rm -v "$WORKSPACE_ESCAPED:/tf" bridgecrew/checkov:latest -d /tf --use-enforcement-rules \
                    -o cli \
                    --prisma-api-url ${PRISMA_API_URL} --bc-api-key ${ACCESS_KEY}::${SECRET_KEY} --repo-id DenisPrisma/DEMOREPO --branch main || true
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                echo "✅ Checkov scan completed. View results in Prisma Cloud."
            }
        }
    }
}
