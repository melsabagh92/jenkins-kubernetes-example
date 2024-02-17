pipeline {
     agent any 
    environment {
        DOCKER_IMAGE = 'melsabagh92/nodejsapp-1.33'
        K8S_NAMESPACE = 'default'
    }
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/melsabagh92/jenkins-kubernetes-example.git']]])
                // Add Maven build steps if required
            }
        }

 
        stage('Build Docker Image') {
            steps {
                script {
 
                        sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
 
        stage('Deploy Docker Image') {
            steps {
                script {
                 withCredentials([string(credentialsId: 'melsabagh', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u melsabagh92 -p ${dockerhubpwd}'
                 }  
                 sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }
 
        stage('Deploy App on k8s') {
         steps {
            withCredentials([
            string(credentialsId: 'my_kubernetes', variable: 'api_token')
            ]) {
             sh 'kubectl --token $api_token --server https://192.168.64.2:8443  --insecure-skip-tls-verify=true apply -f nodejsapp.yaml '
               }
            }
        }
    }
}
