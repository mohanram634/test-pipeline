pipeline {
  
  environment {
    registry = "35.208.178.238:8081/repository/myrepo/"
    registryCredential = 'nexushub'
}
  agent {
    kubernetes {
      label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
      defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
    }
  }
  stages {
    stage('Build') {
      steps {  // no container directive is needed as the maven container is the default
        sh "mvn clean package"   
      }
    }
    
    
 stage('Code Analysis') {
         steps {
                  script {
                    sh "mvn sonar:sonar -Dsonar.host.url=http://192.168.0.108:30303/ -DskipTests=true"
                  }
                }
              }
    
   stage('Build Docker Image') {
     steps {
        container('docker') {  
          withDockerRegistry(credentialsId: 'nexushub', url: 'http://35.208.178.238:8083/repository/myrepo/') {
          sh "docker build -t 35.208.178.238:8083/repository/myrepo/promo-app:dev ."  // when we run docker in this step, we're running it via a shell on the docker build-pod container, 
          sh "docker push 35.208.178.238:8083/repository/myrepo/promo-app:dev"        // which is just connecting to the host docker deaemon
}
                    
        }
      }
    }
  }
}
