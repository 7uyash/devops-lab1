pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')
    }

    environment {
        PY         = 'C:\\Python312\\python.exe'
        DEPLOY_DIR = 'C:\\Users\\7uyas\\deploy\\devops-lab1'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/7uyash/devops-lab1.git'
            }
        }
        stage('Build') {
            steps {
                bat '%PY% -m py_compile app.py test_app.py'
            }
        }
        stage('Test') {
            steps {
                bat '%PY% -m pytest -v --junitxml=test-results.xml'
            }
        }
        stage('Package') {
            steps {
                powershell 'Compress-Archive -Force -Path app.py,test_app.py,README.md -DestinationPath "devops-lab1-build-$env:BUILD_NUMBER.zip"'
            }
        }
        stage('Deploy') {
            steps {
                bat 'if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"'
                powershell 'Expand-Archive -Force -Path "devops-lab1-build-$env:BUILD_NUMBER.zip" -DestinationPath $env:DEPLOY_DIR'
                bat 'cd /d "%DEPLOY_DIR%" && %PY% -c "import app; print(\'Smoke test add(2,3) =\', app.add(2, 3))"'
            }
        }
    }

    post {
        always {
            junit 'test-results.xml'
        }
        success {
            archiveArtifacts artifacts: '*.zip', fingerprint: true
            echo "SUCCESS: build #${env.BUILD_NUMBER} deployed to ${env.DEPLOY_DIR}"
        }
        failure {
            echo 'FAILURE: deployment skipped'
        }
    }
}
