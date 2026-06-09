pipeline {
  agent any

  options {
    timestamps()
  }

  environment {
    CI = 'true'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install') {
      steps {
        sh 'npm ci'
        sh 'npm ci --prefix frontend'
      }
    }

    stage('Test') {
      steps {
        sh 'npm test'
      }
    }

    stage('Build') {
      steps {
        sh 'npm run build'
      }
    }
  }

  post {
    success {
      echo 'Pipeline completed successfully.'
    }
    failure {
      echo 'Pipeline failed. Check the console output and stage logs.'
    }
    always {
      echo 'Pipeline finished.'
    }
  }
}
