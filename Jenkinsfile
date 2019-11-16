pipeline {
  agent any

  environment {
    DOCKER_REGISTRY_CREDENTIALS = '700103be-188b-4f0b-a4f2-722130fb30b1'
    DOCKER_REGISTRY_USERNAME = 'ce3d51cb45a2'
    DOCKER_REGISTRY = 'index.docker.io'
    DOCKER_REGISTRY_URL = "https://${DOCKER_REGISTRY}/v1/"
    PROJECT_IMAGE = "${DOCKER_REGISTRY}/${DOCKER_REGISTRY_USERNAME}/react-app"

    APP_IMAGE = "node:stretch-slim"

    GIT_COMMIT = ''
  }

  stages {
    stage('Preparation') {
      steps {
        echo "--- Get latest git commit ---"
        script {
            GIT_COMMIT = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
        }
      }
    } 

    stage('Test') {
      agent {
        docker { image APP_IMAGE }
      }
      steps {
        sh 'yarn'
        sh 'yarn test'
      }
    }            

    stage('Build Image') {
      steps {
        echo '--- Building image ---'
        sh "docker build -t ${PROJECT_IMAGE}:${GIT_COMMIT} ."
      }
    }

    stage('Push Image') {
      stages {
        stage('Publish Image') {
          steps {
            echo '--- Publishing image ---'
            withDockerRegistry(credentialsId: DOCKER_REGISTRY_CREDENTIALS, url: DOCKER_REGISTRY_URL ) {
              sh "docker push ${PROJECT_IMAGE}:${GIT_COMMIT}"
            }
          }
        }
      }
    }
  }
}
