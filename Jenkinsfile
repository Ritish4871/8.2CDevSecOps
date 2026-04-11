pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        SONAR_HOST_URL = 'https://sonarcloud.io'
    }

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

        stage('SonarCloud Analysis') {
            steps {
                sh '''
                    set -e

                    rm -rf sonar-scanner-8.0.1.6346-linux-x64 sonar-scanner.zip

                    curl -fL -o sonar-scanner.zip \
                      https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-8.0.1.6346-linux-x64.zip

                    unzip -q sonar-scanner.zip

                    export PATH="$WORKSPACE/sonar-scanner-8.0.1.6346-linux-x64/bin:$PATH"
                    export SONAR_HOST_URL="https://sonarcloud.io"

                    sonar-scanner -X
                '''
            }
        }
    }
}