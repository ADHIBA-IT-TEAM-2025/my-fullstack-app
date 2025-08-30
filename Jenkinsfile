pipeline {
  agent any

  environment {
    BACKEND_IMAGE  = 'sanjeev26082002/backend'
    FRONTEND_IMAGE = 'sanjeev26082002/frontend'
  }

  stages {
    stage('Checkout') {
      steps {
        // Checkout repo (SCM requires Pipeline script from SCM job config)
        checkout scm
        script {
          // Get short commit hash for tagging
          SHORT_COMMIT = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
          env.IMAGE_TAG = SHORT_COMMIT
          echo "Using image tag: ${env.IMAGE_TAG}"
        }
      }
    }

    stage('Build Backend') {
      steps {
        dir('backend') {
          sh "docker build -t ${env.BACKEND_IMAGE}:${env.IMAGE_TAG} ."
          sh "docker tag ${env.BACKEND_IMAGE}:${env.IMAGE_TAG} ${env.BACKEND_IMAGE}:latest || true"
        }
      }
    }

    stage('Build Frontend') {
      steps {
        dir('frontend') {
          sh "docker build -t ${env.FRONTEND_IMAGE}:${env.IMAGE_TAG} ."
          sh "docker tag ${env.FRONTEND_IMAGE}:${env.IMAGE_TAG} ${env.FRONTEND_IMAGE}:latest || true"
        }
      }
    }
checkout([$class: 'GitSCM',
  branches: [[name: '*/main']],
  doGenerateSubmoduleConfigurations: false,
  extensions: [[$class: 'WipeWorkspace']], // <-- cleans workspace first
  userRemoteConfigs: [[url: 'https://github.com/ADHIBA-IT-TEAM-2025/my-docker-app.git']]
])

    stage('Docker Login & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          // Login using Jenkins credentials
          sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
          // Push backend images
          sh "docker push ${env.BACKEND_IMAGE}:${env.IMAGE_TAG}"
          sh "docker push ${env.BACKEND_IMAGE}:latest"
          // Push frontend images
          sh "docker push ${env.FRONTEND_IMAGE}:${env.IMAGE_TAG}"
          sh "docker push ${env.FRONTEND_IMAGE}:latest"
        }
      }
    }
  }

  post {
    always {
      // Logout from Docker Hub
      sh 'docker logout || true'
    }
  }
}
