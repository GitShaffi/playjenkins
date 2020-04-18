pipeline {

  environment {
    registry = "myharbor.com:30002/public-project/endgame"
    registryCredential = 'myharbor'
    dockerImage = ""
  }

  agent any

  stages {
    stage('Checkout Source') {
      steps {
        git 'https://github.com/GitShaffi/playjenkins.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }

    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "http://myharbor.com:30002", registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }

    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }
  }
}
