pipeline {
  agent any
  stages {
    stage('prepare') {
      steps {
        parallel(
          "prepare": {
            sh 'echo "zork"'
            sh 'npm install'
            
          },
          "par": {
            dir(path: 'the-wd') {
              sh 'npm install'
            }
            
            
          }
        )
      }
    }
    stage('build') {
      steps {
        sh 'echo "bork"'
      }
    }
  }
}