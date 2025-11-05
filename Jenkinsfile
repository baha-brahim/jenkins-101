pipeline {
  agent any
  environment {
    IMAGE = "yourname/your-app:${env.BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build Python env & Test') {
      steps {
        sh 'python3 -m venv .venv'
        sh '. .venv/bin/activate && pip install -r requirements.txt'
        sh '. .venv/bin/activate && pytest -q'
      }
    }
    stage('Build Docker Image') {
      when { expression { fileExists('Dockerfile') } }
      steps {
        sh "docker build -t ${IMAGE} ."
      }
    }
    stage('Push Image') {
      when { expression { env.DOCKER_REGISTRY != null } }
      steps {
        withCredentials([string(credentialsId: 'docker-registry-token', variable: 'TOKEN')]) {
          sh "echo $TOKEN | docker login -u user --password-stdin $DOCKER_REGISTRY"
          sh "docker tag ${IMAGE} ${DOCKER_REGISTRY}/${IMAGE}"
          sh "docker push ${DOCKER_REGISTRY}/${IMAGE}"
        }
      }
    }
  }
  post {
    always {
      junit '**/target/surefire-reports/*.xml' // adjust to your test reports
      archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true
    }
  }
}
