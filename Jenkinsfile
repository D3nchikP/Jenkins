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
                    # Fix workspace path with quotes to handle spaces
                    WORKSPACE_ESCAPED="${WORKSPACE}"

                    # DEBUG: Show scanned files
                    echo "📂 Listing Terraform files in workspace:"
                    find "$WORKSPACE_ESCAPED" -name "*.tf"

                    # Run Checkov inside Docker (Fixes path issues)
                    docker run --rm -v "$WORKSPACE_ESCAPED:/tf" bridgecrew/checkov:latest -d /tf --use-enforcement-rules \
                    -o cli \
                    --prisma-api-url ${PRISMA_API_URL} --bc-api-key ${ACCESS_KEY}::${SECRET_KEY} \
                    --repo-id DenisPrisma/DEMOREPO --branch main || true

                    # DEBUG: Check if Prisma URL is being generated
                    echo "✅ Checkov scan completed. View results in Prisma Cloud."
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                echo "✅ Pipeline completed. Check Prisma Cloud for results."
            }
        }
    }
}
