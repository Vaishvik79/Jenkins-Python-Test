pipeline {
    agent any

    options {
        skipDefaultCheckout(true)   // prevent auto checkout
    }

    parameters {
        string(name: 'BUILD_TYPE', defaultValue: '', description: 'Detected build type')
    }

    stages {

        stage('Checkout') {
            steps {
                cleanWs()           // clean previous workspace safely
                checkout scm        // single, clean repo checkout
            }
        }

        stage('Discovery') {
            steps {
                sh '''
                echo "Repository structure:"
                tree -L 4 || ls -R
                tree -L 4 > structure.txt || ls -R > structure.txt
                '''
            }
        }

        stage('Build') {
            when {
                expression { params.BUILD_TYPE == 'python' }
            }
            steps {
                sh '''
                python3 --version
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                pip install -e .
                pytest
                pip install build
                python -m build
                echo "Build artifact created successfully"
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'structure.txt', fingerprint: true
            archiveArtifacts artifacts: 'dist/*.whl', fingerprint: true
        }
    }
}
