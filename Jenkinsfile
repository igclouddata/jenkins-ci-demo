pipeline {
  agent any
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Build')    { steps { sh 'echo Construyendo…' } }
    stage('Test')     { steps { sh 'echo Tests OK' } }
  }
  post {
    success { echo '✅ OK' }
    failure { echo '❌ Falló' }
  }
}
