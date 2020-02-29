pipeline {

  environment {
    PROJECT = "jenkins-cd-269710"
    APP_NAME = "node-app"
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "us-central1-a"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BUILD_NUMBER}"
    JENKINS_CRED = "k8s-build-deploy"    
  }

  agent {
    kubernetes {
      label 'ci'
      defaultContainer 'jnlp'
      yamlFile 'build-pod.yaml'
    }
  }
  stages {
    stage('kubectl get pods - checking authorization') {
      steps {
        container('kubectl') {
          sh "kubectl get pods"
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('docker') {
          sh "docker build -t warolv/node-app:${env.BUILD_NUMBER} ."
          sh "docker push warolv/node-app:${env.BUILD_NUMBER}"
        }   
      }
    }
  }
}