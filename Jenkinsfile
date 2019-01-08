pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh "echo 'Build step'"
        //git(url: 'git@github.com:dpriches/davertest.git', branch: 'master')
      }
    }
  }
  environment {
    any = 'test'
  }
}
