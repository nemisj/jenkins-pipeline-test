// https://jenkins.io/blog/2017/01/19/converting-conditional-to-pipeline/
//  https://github.com/jenkinsci/pipeline-model-definition-plugin/wiki/Syntax-Reference
// https://jenkins.io/doc/book/pipeline/jenkinsfile/

pipeline {
  agent any

  environment {
    BITBUCKET_ID = '7f3032d8-047c-4280-98e3-aadc0a9c7405'
  }

  stages {
    stage('install') {
      steps {
        bitbucketStatusNotify(buildState: 'INPROGRESS', credentialsId: env.BITBUCKET_ID)
        script {
          env.NODEJS_HOME = "${tool 'NodeJS 6.9.0'}"
          env.PATH="${env.NODEJS_HOME}:${env.PATH}"
        }

        script { currentBuild.displayName = "#${env.GIT_BRANCH}" }
        sh 'npm prune'
        sh 'npm install'
        sh 'mkdir -p log'
      }
    }

    stage('approve-migrations') {
      when {
        expression {
          env.GIT_BRANCH == 'develop'
        }
      }
      steps {
        sh 'git branch -D develop'
        sh 'git checkout -b develop'
        sh 'npm run migration:approve'
        sh 'git add migrations/*'
        sh 'git rm migrations/999*'
        sh(script: 'git commit -m "jenkins: approving migrations" -n', returnStatus: true)
        sh 'git push origin develop'
      }
    }

    stage('unit-tests') {
      environment {
        MOCHA_FILE = './reports/junit.xml'
      }
      steps {
        sh 'npm run test -- --reporter mocha-junit-reporter'
      }
    }
  }

  post {
    always {
      junit(allowEmptyResults: true, testResults: 'reports/*.xml')
      step([$class: 'CoberturaPublisher', coberturaReportFile: 'reports/coverage/*.xml'])
    }

    success {
      bitbucketStatusNotify(buildState: 'SUCCESSFUL', credentialsId: env.BITBUCKET_ID)
    }

    failure {
      bitbucketStatusNotify(buildState: 'FAILED', credentialsId: env.BITBUCKET_ID)
    }
  }
}
