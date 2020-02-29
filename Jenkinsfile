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
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: jenkins-tool
  containers:
  - name: golang
    image: golang:1.10
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
  - name: docker
      image: docker:18.06.1
      command: ["tail", "-f", "/dev/null"]
      imagePullPolicy: Always
      volumeMounts:
        - name: docker
          mountPath: /var/run/docker.sock # We use the k8s host docker engine
  volumes:
    - name: docker
      hostPath:
        path: /var/run/docker.sock
"""
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