pipeline {
  agent any 
  
  environment {
      TAG = sh(returnStdout: true, script: 'cd spring-boot-sample/src && git rev-parse --short=6 HEAD').trim()
  }

  stages {
    stage('Clone sourc code') {
      steps {
        sh "rm -rf spring-boot-sample"
        sh "git clone https://github.com/shredped/spring-boot-sample.git"
      }
    }

    stage('Test') {
      steps {
        sh "cd spring-boot-sample/src && ./mvnw test"
      }
    }

    stage('Build & Push image') {
      steps {
        sh 'cd spring-boot-sample && docker build -t 172.17.0.1:32000/spring-boot-sample:$(echo $TAG) .'
        sh 'docker push 172.17.0.1:32000/spring-boot-sample:$(echo $TAG)'
      }
    }
    
    stage('Deploy to microk8s') {
      steps {
        sh 'kubectl --kubeconfig /etc/kubeconfig --insecure-skip-tls-verify apply -f spring-boot-sample/k8s' 
      }
    }
  }
}
