pipeline {
  environment {
    imagename = "shivanjalikaname007/flask-hello-world"
    registryCredential = 'as-docker-hub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git([url: 'https://github.com/devandsecops/flask-hello-world.git', branch: 'master'])

      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build imagename
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push("$BUILD_NUMBER")
             dockerImage.push('latest')

          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $imagename:$BUILD_NUMBER"
         sh "docker rmi $imagename:latest"

      }
    }
    stage('Deploy pod on k8s') {
      steps{
        sshagent(['k8s']) {
            sh "scp -o StrictHostKeyCheaking=no kubernetes.yaml ec2-user@172.31.52.80:/home/ec2-user/"
            script{
                try{
                  sh "ssh ec2-user@172.31.52.80 kubectl apply -f ."
                }catch(error){
                    sh "ssh ec2-user@172.31.52.80 kubectl create -f ."
                }


            }
      }
    }
   }
  }
}

