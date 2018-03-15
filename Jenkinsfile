pipeline {
  agent any
  environment {
          REGISTRY_AUTH = credentials("docker-hub-credentials")
          REGISTRY_URL = 'https://registry.hub.docker.com'
          BUILD_TAG = ''
          DOCKER_TLS_VERIFY = 1
          DOCKER_HOST = 'tcp://srv1.buckingham.io:2376'
          DOCKER_CERT_PATH = '/docker_certs'
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
        sshagent (credentials: ['personal-ssh-key']) {
          sh 'ls -lah'
          sh 'scp -o StrictHostKeyChecking=no -r dockprom root@srv1.buckingham.io:/home/'
          sh 'ssh -o StrictHostKeyChecking=no root@srv1.buckingham.io uname -a'
          sh 'ls -lah'
          sh 'ADMIN_USER=admin ADMIN_PASSWORD=admin docker-compose up -d'
        }
      }
    }
  }
  post {
      always {
        sh 'echo "SHOULD MESSAGE SLACK HERE"'
      }
      failure {
        sh "docker login -u=$REGISTRY_AUTH_USR -p=$REGISTRY_AUTH_PSW $REGISTRY_URL"
        sh 'docker-compose -f docker-compose.yml down'
      }
  }
}
