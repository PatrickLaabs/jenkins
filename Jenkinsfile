pipeline {
  agent any
    environment {
        GO111MODULE = 'on'
        CGO_ENABLED = 0
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    }
    tools {
        go 'go-1.17.7'
    }

  stages {
      stage('Preperation and Cleaning workdir') {
          steps {
              // Export environment variables pointing to the directory where Go was installed
              withEnv(["GOBIN=${JENKINS_HOME}/org.jenkinsci.plugins.golang.GolangInstallation/go-1.17.7/bin"]) {
                  sh 'go version'
              }
              sh 'rm -rf $JENKINS_HOME/bolt_exec_puppet'
              sh 'rm -rf $JENKINS_HOME/bolt_exec_puppet/tools'
          }
      }

    stage('Creation of directories') {
        steps {
            echo 'preparing for directories..'
            sh 'mkdir $JENKINS_HOME/bolt_exec_puppet'
            sh 'mkdir $JENKINS_HOME/bolt_exec_puppet/tools'
        }
    }

    stage('Build') {
      steps {
        echo 'Building..'
        sh 'go build'
        echo 'copying jenkins binary to tools dir'
        sh 'cp $JENKINS_HOME/workspace/jenkins-test/jenkins $JENKINS_HOME/bolt_exec_puppet/tools'
        echo 'copying tools dir files into dest tools dir'
        sh 'cp $JENKINS_HOME/workspace/jenkins-test/toolstemp/* $JENKINS_HOME/bolt_exec_puppet/tools'
        echo 'copying config.yaml to dest dir'
        sh 'cp $JENKINS_HOME/workspace/jenkins-test/config.yaml $JENKINS_HOME/bolt_exec_puppet'
      }
    }

    stage('Goquette') {
        steps {
            sh 'go install github.com/PatrickLaabs/goquette@latest'
            echo 'running goquette inside dest dir'
            sh 'cd $JENKINS_HOME/bolt_exec_puppet && $GOBIN/goquette'
            }
        }

    stage('DeployToNexus') {
        steps {
            echo 'deploying to nexus..'
        }
    }
  }
}