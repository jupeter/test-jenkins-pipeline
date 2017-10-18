#!groovy

node {
    stage('Checkout') {
        def scmVars = checkout scm

        echo "Job name: ${env.JOB_NAME} (like jupeter/test-jenkins-pipeline/master)"
        echo "Base name: ${env.JOB_BASE_NAME} (like jupeter)"
    }

    stage('Docker in docker test') {
        node('ecs-node') {
            node('ecs-java') {
                echo "a"
            }

        }
    }
}