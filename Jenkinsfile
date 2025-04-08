pipeline {
    agent { label 'python' } 
    stages {
        stage('Build') {
            steps {
                // Create virtual environment if not exists
                sh 'python -m venv venv'
                // Activate and install dependencies (if any requirements.txt)
                sh './venv/bin/pip install --upgrade pip'
                sh './venv/bin/pip install -r requirements.txt || true' // just in case no file
                // Compile
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            steps {
                sh './venv/bin/pip install pytest' // ensure pytest is available
                sh './venv/bin/pytest --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
                // Install pyinstaller
                sh './venv/bin/pip install pyinstaller'
                // Create binary
                sh './venv/bin/pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
