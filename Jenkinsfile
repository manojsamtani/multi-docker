pipeline {
  environment {
    registry = "manojsamtani/apache"
    registryCredential = 'docker-registry-credentials'
    dockerImage = ''
  }
  agent any

  stages {

    stage('Build and start test image') {
      steps{
        echo "Starting to build docker images $registry:$GIT_COMMIT"
        script {
          sh "docker-compose build"
          sh "docker-compose up -d"
        }
      }
    }

    stage('Test run image') {
      steps{
        script {
          sh "docker-compose ps"
          input("Verify in your browser, if worked fine then proceed ?")
        }
      }
    }

    //stage('Push Image - Upload to Registry/DockerHUB') {
    //  when {
    //    branch 'master'
    //  }
    //  steps{
    //    script {
//          if ( "${BRANCH_NAME}" == 'master' ) {
    //        docker.withRegistry( '', registryCredential ) {
    //          dockerImage.push()
    //          dockerImage.push("latest")
    //        }
//          }
    //    }
    //  }
    //}

    //stage('Remove unsed docker image') {
    //  steps{
    //    input message: "Removing $registry:${BRANCH_NAME}_${GIT_COMMIT} - Click Proceed to continue -"
    //    sh "docker rmi $registry:${BRANCH_NAME}_${GIT_COMMIT}"
    //  }
    //}
  }

  post {
    success {
      echo "Build completed successfully :-)"
    }
  }
}
