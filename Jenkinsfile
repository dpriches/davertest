pipeline {
  agent any
  stages {
    stage('scm') {
      steps {
        git(url: 'git@github.com:dpriches/davertest.git', branch: 'master')
      }
    }
  }
  environment {
    any = 'test'
  }
}