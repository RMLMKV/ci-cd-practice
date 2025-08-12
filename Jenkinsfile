pipeline {
    agent {
        docker {
            image 'python:3.11'  // официальный образ Python
            args '-u'            // чтобы вывод шёл сразу
        }
    }

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
                    python --version
                    python -m venv $VENV_DIR
                    . $VENV_DIR/bin/activate
                    pip install --upgrade pip
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                '''
            }
        }

        stage('Run tests') {
            steps {
                sh '''
                    . $VENV_DIR/bin/activate
                    python app.py
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Деплой (пока заглушка)"
            }
        }
    }

    post {
        always {
            echo "🧹 Очистка"
            sh 'rm -rf $VENV_DIR'
        }
    }
}
