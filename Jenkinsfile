pipeline {
  agent any
  environment {
    DOCKER_REPO = "anugrahamanoharan16-creator/simple-cicd-app"
    DOCKER_CRED_ID = "dockerhub-creds"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build & Push Docker Image') {
      steps {
        script {
          def tag = "build-${env.BUILD_NUMBER}"
          sh "docker build -t ${DOCKER_REPO}:${tag} ."
          withCredentials([usernamePassword(credentialsId: "${DOCKER_CRED_ID}", usernameVariable: 'USER', passwordVariable: 'PASS')]) {
            sh '''
              echo "$PASS" | docker login -u "$USER" --password-stdin
              docker push ${DOCKER_REPO}:${tag}
              docker logout
            '''
          }
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          kubectl set image deployment/simple-cicd-app simple-cicd-app=${DOCKER_REPO}:${BUILD_NUMBER} --record || true
        '''
      }
    }
  }
}
