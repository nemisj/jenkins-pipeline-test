pipeline {
  agent any
  stages {
    stage('install') {
      steps {
        git(url: 'ssh://git@altssh.bitbucket.org:443/gripcode/grip-core.git', branch: '${env.BRANCH}')
        nodejs(nodeJSInstallationName: 'Node 6.9.0')
        sh 'npm prune'
        sh 'npm install'
        sh 'mkdir -p log'
      }
    }
    stage('approve-migrations') {
      steps {
        script {
          if (env.BRANCH == 'develop') {
            sh 'git co -b develop'
            sh 'npm run migration:approve'
            sh 'git add migrations/*'
            sh(script: 'git commit -m "jenkins: approving migrations" -n', returnStatus: true)
            sh 'git push origin develop'
          }
        }
        
      }
    }
  }
  environment {
    BRANCH = ''
  }
}