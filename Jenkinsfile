pipeline {
  agent any
  environment {
    PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
    IMAGE_NAME = "${env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'}"
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'npm install'
      }
    }
    stage('Test') {
      steps {
        sh 'npm test'
      }
    }
    stage('Replace Logo') {
      steps {
        sh "cp assets/${env.BRANCH_NAME}_logo.svg src/logo.svg"
      }
    }
    stage('Docker Build') {
      steps {
        sh "docker build -t ${IMAGE_NAME} ."
      }
    }
    stage('Push') {
      steps {
        withDockerRegistry([credentialsId: 'dockerhub-creds']) {
          sh "docker tag ${IMAGE_NAME} yourdockerhub/${IMAGE_NAME}"
          sh "docker push yourdockerhub/${IMAGE_NAME}"
        }
      }
    }
    stage('Trigger Deploy') {
      steps {
        build job: "${env.BRANCH_NAME == 'main' ? 'Deploy_to_main' : 'Deploy_to_dev'}"
      }
    }
  }
}
