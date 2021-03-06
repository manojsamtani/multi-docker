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
          if ( "${BRANCH_NAME}" == 'master' ) {
            dockerfile = 'Dockerfile'
          } else {
            dockerfile = 'Dockerfile.dev'
          }
          sh "export DFILE=${dockerfile}; /usr/local/bin/docker-compose -p $GIT_COMMIT up -d --build"
        }
      }
    }

    stage('Test run image') {
      steps{
        script {
          sh "export DFILE=${dockerfile}; /usr/local/bin/docker-compose -p $GIT_COMMIT ps"
        }
      }
    }

    stage('Build Images') {
      steps {
        timeout(time: 5, unit: 'MINUTES') {
          input message: 'Does Pre-Production look good? Ready to Build images?', ok: 'yes'
        }
        script {
          sh "docker build -t manojsamtani/multi-worker ./worker"
          sh "docker build -t manojsamtani/multi-nginx ./nginx"
          sh "docker build -t manojsamtani/multi-server ./server"
          sh "docker build -t manojsamtani/multi-client ./client"
        }
//        script {
//         if ( "${BRANCH_NAME}" == 'develop' ) {
//            sh "git push origin master"
//          }
//        }
      }
    }

    stage('Push Images to Docker HUB') {
      steps {
        sh "docker push manojsamtani/multi-worker"
        sh "docker push manojsamtani/multi-nginx"
        sh "docker push manojsamtani/multi-server"
        sh "docker push manojsamtani/multi-client"
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
    always {
      sh "/usr/local/bin/docker-compose -p $GIT_COMMIT down || true"
    }

    failure {
      echo "Build FAILED"
    }

    success {
      echo "Build Success"
    }
  }
}
