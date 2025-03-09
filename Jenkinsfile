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
                git branch: 'master', url: 'https://github.com/D3nchikP/terragoat.git'
            }
        }

        stage('Checkov Scan') {
            steps {
                script {
                    sh '''
                    # Use Bash explicitly
                    export WORKSPACE_ESCAPED="$(echo $WORKSPACE | sed 's/ /\\\\ /g')"

                    # Run Checkov inside Docker with fixed path
                    docker run --rm -v "$WORKSPACE_ESCAPED/terraform/aws:/tf" bridgecrew/checkov:latest -d /tf --use-enforcement-rules \
                    -o cli -o junitxml --output-file-path console,"$WORKSPACE/results.xml" \
                    --prisma-api-url ${PRISMA_API_URL} --bc-api-key ${ACCESS_KEY}::${SECRET_KEY} --repo-id D3nchikP/terragoat --branch master || true
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                if (fileExists("${WORKSPACE}/results.xml")) {
                    junit skipPublishingChecks: true, testResults: '${WORKSPACE}/results.xml'
                } else {
                    echo "⚠️ Warning: No results.xml found. Checkov scan may have failed."
                }
            }
        }
    }
}
