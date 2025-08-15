pipeline {
  agent { label 'py' }   // наш подключенный агент

  options {
    timestamps()
  }

  environment {
    VENV_DIR = '.venv'
  }

  stages {
    stage('Checkout') {
      steps {
        // Jenkins сам возьмёт repo из настроек Job (SCM)
        checkout scm
      }
    }

    stage('Setup Python venv') {
      steps {
        sh '''
          set -eux
          python --version
          python -m venv $VENV_DIR
          . $VENV_DIR/bin/activate
          pip install --upgrade pip
        '''
      }
    }

    stage('Install deps') {
      steps {
        sh '''
          set -eu
          . $VENV_DIR/bin/activate
          if [ -f requirements.txt ]; then
            pip install -r requirements.txt
          else
            echo "requirements.txt not found, skipping"
          fi
        '''
      }
    }

    stage('Lint (flake8)') {
      when { expression { return fileExists('requirements.txt') } }
      steps {
        sh '''
          set -eu
          . $VENV_DIR/bin/activate
          if grep -qi '^flake8' requirements.txt 2>/dev/null; then
            flake8 || true
          else
            echo "flake8 not in requirements, skipping"
          fi
        '''
      }
    }

    stage('Tests (pytest)') {
      steps {
        sh '''
          set -eu
          . $VENV_DIR/bin/activate
          if [ -f pytest.ini ] || [ -d tests ] || ls -1 test_*.py 2>/dev/null | head -n1 >/dev/null; then
            pytest -q --maxfail=1 --disable-warnings || (echo "Tests failed" && exit 1)
          else
            echo "No tests found, running app.py if exists"
            [ -f app.py ] && python app.py || echo "app.py not found, skipping"
          fi
        '''
      }
    }

    stage('Archive artifacts') {
      steps {
        sh 'mkdir -p build && echo "Build at $(date)" > build/BUILD_INFO.txt'
        archiveArtifacts artifacts: 'build/**', fingerprint: true
      }
    }
  }

  post {
    always {
      echo '🧹 cleanup venv'
      sh 'rm -rf $VENV_DIR || true'
    }
  }
}
