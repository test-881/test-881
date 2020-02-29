pipeline {

  environment {
    PROJECT = "jenkins-cd-269710"
    APP_NAME = "node-app"
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "us-central1-a"
    IMAGE_TAG = "warolv/node-app:${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"    
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
          step([$class: 'KubernetesEngineBuilder',namespace: 'production', projectId: env.PROJECT, clusterName: env.CLUSTER, zone: env.CLUSTER_ZONE, manifestPattern: 'k8s/deployment.yaml', credentialsId: env.JENKINS_CRED, verifyDeployments: false])
        }   
      }
    }
  }
}