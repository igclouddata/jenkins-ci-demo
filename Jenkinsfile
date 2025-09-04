pipeline {
  agent {
    docker { image 'python:3.11-slim' }
  }
  options { timestamps(); disableConcurrentBuilds() }

  stages {
    stage('Checkout') {
      steps {
        deleteDir() // limpia workspace
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/main']],
          userRemoteConfigs: [[ url: 'https://github.com/igclouddata/jenkins-ci-demo.git' ]],
          extensions: [[ $class: 'CloneOption', shallow: true, depth: 1, noTags: false ]]
        ])
      }
    }

    stage('Deps') {
      steps {
        sh 'python -V'
        sh 'python -m pip install --upgrade pip'
        sh 'python -m pip install --no-cache-dir -r requirements.txt'
      }
    }

    stage('Unit tests') {
      steps { sh 'pytest -q --maxfail=1 --disable-warnings' }
    }

    stage('Package') {
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
