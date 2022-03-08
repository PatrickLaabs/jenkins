pipeline {
  agent any
    environment {
        GO111MODULE = 'on'
        CGO_ENABLED = 0
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
        PATH = "$GOPATH/bin"
    }
    tools {
        go 'go-1.17.7'
    }

  stages {
    stage('Preperation') {
        steps {
            sh 'go install github.com/PatrickLaabs/goquette@latest'
        }
    }
    stage('Build') {
      steps {
        echo 'Building..'
        sh 'go build'
      }
    }
  }
}
