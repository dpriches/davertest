#!/usr/bin/env groovy
def pomVersion = 'Unknown'
def pomArtifactId = 'Unknown'
def pomGroupId = 'Unknown'
def repoUrl = 'https://nexus.equifax.com/repository'
def artifactRepo = 'usis-ic-maven2-snapshots'

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
            }
        }

// If this is the qa job that creates a release, run the commands to create a new branch
        stage('Create Release Branch') {
            steps {
                sh "echo 'Release Branch'"
            }
        }

// Get the Maven GAV info
        stage ('Get Maven GAV info') {
            steps {                                
                script {
                    pomVersion    = sh (script: "cat ${WORKSPACE}/pom.xml | xpath '/project/version/text()'",    returnStdout: true).trim()                     
                    pomArtifactId = sh (script: "cat ${WORKSPACE}/pom.xml | xpath '/project/artifactId/text()'", returnStdout: true).trim()                     
                    pomGroupId    = sh (script: "cat ${WORKSPACE}/pom.xml | xpath '/project/groupId/text()'",    returnStdout: true).trim()                     
                }
            }
        }
        
// Build the app without running tests. Separates compilation errors from test errors       
        stage('Build') {
            steps {
                sh "mvn -Dmaven.test.skip clean package "
            }
        }

// Do some unit testing
        stage('Test') {
            parallel {
                stage ('Unit testing') {
                    steps {
                        sh "mvn test "
                    }
// Archive Junit testing - temporarily unused
//                    post {
//                        always {
//                            junit '**/target/surefire-reports/*.xml'
//                        }
//                    }
                }

                // stage ('OWASP testing') {
                    // steps {
                        // sh "mvn org.owasp:dependency-check-maven:3.1.1:aggregate -Dproxy.server=172.18.100.15 -Dproxy.port=18717 "
                        // sh 'Report="${WORKSPACE}/target/dependency-check-report.html" ; if [ -e "$Report" ] ; then echo "$Report found, copying the report to the reporting server" ; cp -a ${WORKSPACE}/target/dependency-check-report.html "/software/usis/ic/owasp-dc-reports/${pomArtifactId}-${pomVersion}-$(date +"%Y%m%d-%H%M%S").html" ;  else echo "$Report not found." ; fi'
                    // }
                // }

                stage ('Sonar testing') {
                    steps {
                        sh "mvn sonar:sonar -Dsonar.dependencyCheck.htmlReportPath=${WORKSPACE}/target/dependency-check-report.html "
                    }
                }
            }
        }

// Create rpms
// Split app creation from properties creation
// just to show parallel building
      
        stage('Create RPMS') {
            parallel {
                stage ('Create App RPM') {
                    steps {
                        sh "mvn package -P create-app-rpm -f create-rpms/pom.xml "
                    }
                }
                stage ('Create Properties RPMs') {
                    steps {
                        sh "mvn package -P create-properties-rpms -f create-rpms/pom.xml "
                    }
                }
            }
        }

// Upload supporting installation files        
        stage('Upload Install files') {
            parallel {
                stage ('call-rundeck-deploy-job.sh') {
                    steps {
                        sh "echo 'upload rundeck script'"
                        //sh "mvn deploy:deploy-file -Durl=${repoUrl}/${artifactRepo} -DgeneratePom=true -DrepositoryId=nexus-efx -Dfile=_scm-tools/install-scripts/call-rundeck-deploy-job.sh -DgroupId=${pomGroupId}.install-scripts -DartifactId=call-rundeck-deploy-job.sh -Dversion=${pomVersion}"
                    }
                }

                stage ('install.sh') {
                    steps {
                        sh "echo 'upload install script'"
                        //sh "mvn deploy:deploy-file -Durl=${repoUrl}/${artifactRepo} -DgeneratePom=true -DrepositoryId=nexus-efx -Dfile=_scm-tools/install-scripts/install-2.1.sh -DgroupId=${pomGroupId}.install-scripts -DartifactId=install-2.1.sh -Dversion=${pomVersion}"
                    }
                }

                stage ('config.xml') {
                    steps {
                        sh "echo 'upload config.xml'"
                        //sh "mvn deploy:deploy-file -Durl=${repoUrl}/${artifactRepo} -DgeneratePom=true -DrepositoryId=nexus-efx -Dfile=deployments/config.xml  -DgroupId=${pomGroupId}.install-scripts -DartifactId=config.xml -Dversion=${pomVersion}"
                    }
                }
            }
        }

// Install app to selected environments        
        stage ('Deploy') {
            steps {
                sh "echo 'deploy stage'"
            }
        }

// Cleanup workspace
        stage('Cleanup') {
            steps {
                step ([$class: 'WsCleanup'])
            }
        }
    }
}
