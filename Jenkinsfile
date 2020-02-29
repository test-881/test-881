pipeline {

  environment {
    PROJECT = "jenkins-cd-269710"
    APP_NAME = "node-app"
    CLUSTER = "jenkins-play"
    CLUSTER_ZONE = "us-central1-a"
    IMAGE_TAG = "warolv/node-app-x:${env.BUILD_NUMBER}"
  }

  agent {
    kubernetes {
      label 'ci'
      defaultContainer 'jnlp'
      yamlFile 'build-pod.yaml'
    }
  }
  stages {
    stage('Test') {
      steps {
        container('node') {
          sh "npm install"
          sh "chmod +x ./script/test.sh"
          sh "./script/test.sh"
        }
      }
    }
    stage('Build & Push') {
      steps {
        container('docker') {
          sh "docker build -t ${IMAGE_TAG} -t warolv/node-app:latest ."
          sh "docker login -u=warolv -p=typhoon81"
          sh "docker push ${IMAGE_TAG}"
          sh "docker push warolv/node-app:latest"
        }   
      }
    }
    stage('Deploy Production') {
      steps {
        container('kubectl') {
          sh("sed -i 's/node-app:latest/node-app:${env.BUILD_ID}/g' k8s/deployment.yaml")
          sh("kubectl apply -f k8s/deployment.yaml --namespace=production")
        }   
      }
    }
  }
}