pipeline {
  agent any

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  environment {
    PYTHON = "python3"
    IMAGE_NAME = "jenkins-ci-demo"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Setup Python') {
      steps {
        sh '''
          which python3 || (apt-get update && apt-get install -y python3 python3-pip)
          python3 -V
          pip3 install --upgrade pip
          pip3 install -r requirements.txt
        '''
      }
    }

    stage('Unit Tests') {
      steps {
        sh 'pytest -q --maxfail=1 --disable-warnings'
      }
      post {
        always { junit '**/junit*.xml'  // si luego generas JUnit XML
        }
      }
    }

    stage('Package (optional)') {
      when { expression { return fileExists('app.py') } }
      steps {
        sh 'tar -czf artifact.tgz app.py requirements.txt'
        archiveArtifacts artifacts: 'artifact.tgz', fingerprint: true
      }
    }

    // ======= OPCIONAL: construir/push Docker =======
    stage('Docker Build (optional)') {
      when { expression { return fileExists('Dockerfile') } }
      steps {
        sh 'docker version || echo "Docker no disponible dentro del contenedor Jenkins"'
        sh 'if docker version; then docker build -t $IMAGE_NAME:build-$BUILD_NUMBER .; else echo "Saltar build docker"; fi'
      }
    }
    // ===============================================
  }

  post {
    success { echo '✅ CI OK' }
    failure { echo '❌ CI falló' }
  }
}
