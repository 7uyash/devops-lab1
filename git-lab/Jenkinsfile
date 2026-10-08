pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')
    }

    environment {
        PY = 'C:\\Python312\\python.exe'
    }

    stages {
        stage('Pull Source') {
            steps {
                git branch: 'main', url: 'https://github.com/7uyash/devops-git-lab.git'
            }
        }
        stage('Build') {
            steps {
                bat '%PY% -m py_compile app.py login.py'
            }
        }
        stage('Test') {
            steps {
                bat '%PY% app.py'
                bat '%PY% -c "from login import login; assert login(\'Suyash\') == \'Welcome Suyash\'; print(\'login test passed\')"'
            }
        }
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: '*.py, README.md', fingerprint: true
            }
        }
    }

    post {
        success { echo "Build #${env.BUILD_NUMBER} succeeded" }
        failure { echo "Build #${env.BUILD_NUMBER} failed" }
    }
}
