pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ritish4871/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    set +e
                    npm test
                    TEST_EXIT=$?
                    echo "npm test exit code: $TEST_EXIT"
                    echo "Continuing because this task allows the pipeline to continue even if tests fail."
                    exit 0
                '''
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh '''
                    set +e
                    if npm run | grep -q "coverage"; then
                        npm run coverage
                    else
                        echo "No coverage script found in package.json. Skipping coverage generation."
                    fi
                    exit 0
                '''
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh '''
                    set +e
                    npm audit --audit-level=low
                    AUDIT_EXIT=$?
                    echo "npm audit exit code: $AUDIT_EXIT"
                    echo "Continuing so vulnerabilities remain visible in the Jenkins console."
                    exit 0
                '''
            }
        }
    }
}