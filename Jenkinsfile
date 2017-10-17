#!groovy

node {
    stage('Checkout') {
        def scmVars = checkout scm

        echo "Change author: ${env.JOB_NAME}"
    }
}