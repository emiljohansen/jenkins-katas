pipeline {
  environment {
    docker_username = 'emiljohansen'
    DOCKERCREDS = credentials('docker_login')
  }
  agent any
  stages {
    stage('_clone down_') {
      steps {
        stash(excludes: '.git', name: 'code')
      }
    }

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
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash(excludes: '.git', name: 'build')
            sh 'ls -a'
            deleteDir()
            sh 'ls -a'
            skipDefaultCheckout true
          }
        }

        stage('Test App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }
      }
    }

      stage('Docker build') {
        steps {
          unstash 'build' //unstash the repository code
          sh 'ci/build-docker.sh'
          stash(excludes: '.git', name: 'image')
      }
    }

    stage('Docker Push') {
      when {branch "master"}
      steps {
        unstash 'image'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
        sh 'ci/push-docker.sh'
      }
    }

    stage('Component Test') {
      when { branch 'dev/'}
      steps {
        unstash 'build'
        sh 'ci/component-test.sh'
      }
    }
  }
}