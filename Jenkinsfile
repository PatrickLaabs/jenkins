pipeline {
  agent any
    environment {
        GO111MODULE = 'on'
        CGO_ENABLED = 0
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
        PATH = "$PATH:$GOBIN"
        WORKDIR = "bolt_exec_puppet"
    }
    tools {
        go 'go-1.17.7'
    }

  stages {
      stage('Preperation and Cleaning workdir') {
          steps {
              withEnv(["GOBIN=${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}/bin"]) {
              }
              sh 'rm -rf $JENKINS_HOME/$WORKDIR'
              sh 'rm -rf $JENKINS_HOME/$WORKDIR/tools'
          }
      }

    stage('Creation of directories') {
        steps {
            sh 'mkdir $JENKINS_HOME/$WORKDIR'
            sh 'mkdir $JENKINS_HOME/$WORKDIR/tools'
        }
    }

    stage('Build') {
      steps {
        sh 'GOOS=linux go build'
        sh 'GOOS=windows go build'
        sh 'tar cf bolt_exec_puppet.zip *.exe'
        sh 'cp $JENKINS_HOME/workspace/$JOB_NAME/*.zip $JENKINS_HOME/$WORKDIR/tools'
        sh 'cp $JENKINS_HOME/workspace/$JOB_NAME/toolstemp/* $JENKINS_HOME/$WORKDIR/tools'
        sh 'cp $JENKINS_HOME/workspace/$JOB_NAME/config.yaml $JENKINS_HOME/$WORKDIR'
      }
    }

    stage('Goquette - NuGet Packaging') {
        steps {
            sh 'go install github.com/PatrickLaabs/goquette@latest'
            sh 'cd $JENKINS_HOME/$WORKDIR && $JENKINS_HOME/jobs/$JOB_NAME/builds/$BUILD_ID/bin/goquette'
            }
        }

    stage('nFPM - rpm Packaging') {
        steps {
            sh 'go install github.com/goreleaser/nfpm/v2/cmd/nfpm@latest'
            sh '$JENKINS_HOME/jobs/$JOB_NAME/builds/$BUILD_ID/bin/nfpm pkg --packager rpm --target $PWD'
            sh 'cp *.rpm $JENKINS_HOME/$WORKDIR'
        }
    }

    stage('DeployToNexus') {
        steps {
            echo 'deploying to nexus..'
        }
    }
  }
}