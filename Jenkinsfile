pipeline {
  agent any
  environment {
          REGISTRY_AUTH = credentials("docker-hub-credentials")
          REGISTRY_URL = 'https://registry.hub.docker.com'
          BUILD_TAG = ''
  }
  stages{
    stage('Clone repository') {
      steps {
        /* Let's make sure we have the repository cloned to our workspace */
        checkout scm
      }
    }

    stage('Deploy to host') {
      steps {
        withEnv([
            "DOCKER_TLS_VERIFY=1",
            "DOCKER_HOST=tcp://srv1.buckingham.io:2376",
            "DOCKER_CERT_PATH=/docker_certs"]) {
            sh "docker login -u=$REGISTRY_AUTH_USR -p=$REGISTRY_AUTH_PSW $REGISTRY_URL"
            sh 'echo "Deploying to remote docker host"'
            sh 'docker-compose -f docker-compose.yml up -d'
            sh 'echo "Deployed to $DOCKER_HOST"'
        }
      }
    }
  }
}
