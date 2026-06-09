pipeline {
  agent any

  options {
    timestamps()
    timeout(time: 45, unit: 'MINUTES')
  }

  environment {
    CI = 'true'
    COMPOSE_PROJECT = 'tp7'
    REGISTRY_URL = 'localhost:5001'
    BACKEND_IMAGE = 'proshop-backend'
    FRONTEND_IMAGE = 'proshop-frontend'
    VERSION_TAG = 'v1.0'
  }

  stages {
    // Stage 1 - Checkout source code from Git
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    // Stage 2 - Install backend and frontend dependencies
    stage('Install') {
      steps {
        sh '''
          docker run --rm -v "$PWD:/workspace" -w /workspace node:20-alpine \
          sh -lc "npm ci && npm ci --prefix frontend"
        '''
      }
    }

    // Stage 3 - Lint backend code with ESLint
    stage('Lint') {
      steps {
        sh '''
          docker run --rm -v "$PWD:/workspace" -w /workspace node:20-alpine \
          sh -lc "npm ci && npm run lint"
        '''
      }
    }

    // Stage 4 - Run automated tests
    stage('Test') {
      steps {
        sh '''
          docker run --rm -v "$PWD:/workspace" -w /workspace node:20-alpine \
          sh -lc "npm ci && npm test"
        '''
      }
    }

    // Stage 5 - Build and version Docker images then push to local registry
    stage('Build Docker') {
      steps {
        sh '''
          docker build -f backend/Dockerfile \
            -t ${REGISTRY_URL}/${BACKEND_IMAGE}:latest \
            -t ${REGISTRY_URL}/${BACKEND_IMAGE}:${VERSION_TAG} .

          docker build -f frontend/Dockerfile \
            -t ${REGISTRY_URL}/${FRONTEND_IMAGE}:latest \
            -t ${REGISTRY_URL}/${FRONTEND_IMAGE}:${VERSION_TAG} .

          docker push ${REGISTRY_URL}/${BACKEND_IMAGE}:latest
          docker push ${REGISTRY_URL}/${BACKEND_IMAGE}:${VERSION_TAG}
          docker push ${REGISTRY_URL}/${FRONTEND_IMAGE}:latest
          docker push ${REGISTRY_URL}/${FRONTEND_IMAGE}:${VERSION_TAG}
        '''
      }
    }

    // Stage 6 - Deploy full stack with Docker Compose (main branch only)
    stage('Deploy') {
      when {
        branch 'main'
      }
      steps {
        sh 'docker compose -p ${COMPOSE_PROJECT} up -d --build'
      }
    }

    // Stage 7 - Verify service health after deployment with retry
    stage('Health Check') {
      when {
        branch 'main'
      }
      steps {
        retry(6) {
          sleep 10
          sh 'docker exec ${COMPOSE_PROJECT}-nginx-1 wget -qSO- http://127.0.0.1:8080/api/config/paypal -O /dev/null'
        }
      }
    }
  }

  post {
    success {
      echo 'CI/CD pipeline completed successfully.'
    }
    failure {
      echo 'Pipeline failed. Deployment is blocked until errors are fixed.'
    }
    always {
      echo 'Pipeline execution finished.'
    }
  }
}
