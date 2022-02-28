pipeline {
  agent any
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    }
    tools {
        go 'go-1.17.7'
    }

  stages {
    stage('Build') {
      steps {
        echo 'Building..'
        sh 'go build'
      }
    }

    stage ('Release') {
          when {
            buildingTag()
          }

        steps {
            sh 'git git tag -a v0.0.2 -m "First Release" && git push origin v0.0.2'
            sh 'curl -sL https://git.io/goreleaser | bash'
        }
    }
  }
}
