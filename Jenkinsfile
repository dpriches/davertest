pipeline {
    agent any
    
    tools {
        maven 'mvn-3.6.0'
    }
    
    environment {
        // Use repo local to workspace. Not implemented
        //mvn_opts="-Dmaven.repo.local=.repository" 
        
        // Set url, credentials to access build tools repo
        BUILD_TOOLS_REPO_URL = 'ssh://git@bitbucket.equifax.com:7999/usis-ic/ic-saas-build-deploy-tools.git'
        BUILD_TOOLS_CRED_ID  = 'db57d32b-2a0d-431d-8431-ccea782faaa0'
    }

    stages {    

// Do some prep work for the build
        stage('Prep') {            
            steps {
                sh "echo 'Prep stage'"
                //mvn help:evaluate -Dexpression=project.version    -q -DforceStdout
                sh 'mvn package -Dmaven.test.skip'
            }
        }
    }
}
