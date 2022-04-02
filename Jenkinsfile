pipeline {
  environment {
    //registry = "pluhin31/test"
    credential = 'redman27'
    image_mysql = "redman27/wiki_mysql"
    registry = "registry.hub.docker.com"
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git url: 'https://github.com/redman27/mysql_dockerfile.git', branch: 'main', credentialsId: "jenkins_git"
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImageMysql = docker.build image_mysql + ":$BUILD_NUMBER" , "."
          //dockerImage = docker.build registry + ":$BUILD_NUMBER" , "--network host ."
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
//          docker.withRegistry( 'https://$registry', registryCredential ) {
            docker.withRegistry('', 'docker_hub') {
            dockerImageMysql.push("${env.BUILD_ID}")
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $image_mysql:${env.BUILD_ID}"
      }
    }
    stage('Deploy in k8s') {
      steps{
        sh "sed -i 's|image_variable|$image_mysql:${env.BUILD_ID}|g' /var/lib/jenkins/mysql-deployment.yaml"
      }
    }
  }
}
