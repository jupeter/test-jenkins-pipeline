#!groovy

node {
    stage('Checkout') {
        def scmVars = checkout scm

        echo scmVars.GIT_COMMITTER_NAME
    }
}