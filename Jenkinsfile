pipeline {
    agent any

    environment {
        VENV_DIR = '.venv'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                sh '''
                    python -m venv $VENV_DIR
                    . $VENV_DIR/Scripts/activate
                    pip install --upgrade pip
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                '''
            }
        }

        stage('Run tests') {
            steps {
                sh '''
                    . $VENV_DIR/Scripts/activate
                    python app.py
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Деплой (заглушка)"
            }
        }
    }

    post {
        always {
            echo "Очистка"
            sh 'rm -rf $VENV_DIR'
        }
    }
}
