pipeline {
  environment {
    docker_username = 'emiljohansen'
    DOCKERCREDS = credentials('docker_login')
  }
  agent any
  stages {
    stage('Parallel Execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh '''echo "Hello, World!"
'''
          }
        }

        stage('Build App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }

        stage('Test App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }
      }
    }

      stage('Docker push') {
        steps {
        //unstash 'code' //unstash the repository code
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
        sh 'ci/push-docker.sh'
      }
    }

    post {
      always {
        deleteDir() /* clean up our workspace */
      }
  }
}