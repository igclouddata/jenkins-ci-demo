pipeline {
  agent {
    docker { image 'python:3.11-slim' }   // corre todo dentro del contenedor
  }
  options { timestamps(); disableConcurrentBuilds() }

  stages {
    stage('Checkout')   { steps { checkout scm } }
    stage('Deps')       {
      steps {
        sh 'python -V'
        sh 'python -m pip install --upgrade pip'
        sh 'python -m pip install --no-cache-dir -r requirements.txt'
      }
    }
    stage('Unit tests') { steps { sh 'pytest -q --maxfail=1 --disable-warnings' } }
    stage('Package')    {
      steps {
        sh 'tar -czf artifact.tgz app.py requirements.txt'
        archiveArtifacts artifacts: 'artifact.tgz', fingerprint: true
      }
    }
  }

  post {
    success { echo '✅ CI OK' }
    failure { echo '❌ CI falló' }
  }
}
