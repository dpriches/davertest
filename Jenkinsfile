pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        script { 
          sh "echo 'Build step'"
          //git(url: 'git@github.com:dpriches/davertest.git', branch: 'master')
          git credentialsId: 'github-personal', url: 'https://github.com/dpriches/davertest.git'
          sh "git rev-parse HEAD > .git/commit-id"
          def commit_id = readFile ('.git/commit-id').trim ()
          println commit_id
        }
      }
    }
  }
  environment {
    any = 'test'
  }
}
