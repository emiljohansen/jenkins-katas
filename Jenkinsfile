pipeline {
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

        stage('error') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            sh 'source ./ci/build-app.sh'
          }
        }

      }
    }

  }
}