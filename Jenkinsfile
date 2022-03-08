pipeline {
  agent any
    environment {
        GO111MODULE = 'on'
        CGO_ENABLED = 0
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
        PATH = "$PATH:$GOBIN"
    }
    tools {
        go 'go-1.17.7'
    }

  stages {
      stage('Preperation and Cleaning workdir') {
          steps {
              // Export environment variables pointing to the directory where Go was installed
              withEnv(["GOBIN=${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}/bin"]) {
              }
              sh 'rm -rf $JENKINS_HOME/bolt_exec_puppet'
              sh 'rm -rf $JENKINS_HOME/bolt_exec_puppet/tools'
          }
      }

    stage('Creation of directories') {
        steps {
            sh 'mkdir $JENKINS_HOME/bolt_exec_puppet'
            sh 'mkdir $JENKINS_HOME/bolt_exec_puppet/tools'
        }
    }

    stage('Build') {
      steps {
        sh 'go build'
        sh 'cp $JENKINS_HOME/workspace/$JOB_NAME/jenkins $JENKINS_HOME/bolt_exec_puppet/tools'
        sh 'cp $JENKINS_HOME/workspace/$JOB_NAME/toolstemp/* $JENKINS_HOME/bolt_exec_puppet/tools'
        sh 'cp $JENKINS_HOME/workspace/$JOB_NAME/config.yaml $JENKINS_HOME/bolt_exec_puppet'
      }
    }

    stage('Goquette') {
        steps {
            sh 'go install github.com/PatrickLaabs/goquette@latest'
            sh 'cd $JENKINS_HOME/bolt_exec_puppet && $JENKINS_HOME/jobs/$JOB_NAME/builds/$BUILD_ID/bin/goquette'
            }
        }

    stage('DeployToNexus') {
        steps {
            echo 'deploying to nexus..'
        }
    }
  }
}