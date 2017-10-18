#!groovy

node {
    stage('Checkout') {
        def scmVars = checkout scm

        echo "Job name: ${env.JOB_NAME} (like jupeter/test-jenkins-pipeline/master)"
        echo "Base name: ${env.JOB_BASE_NAME} (like jupeter)"
    }

    stage('Docker in docker test') {
        node('ecs-node') {
            docker.image("mysql:5.7.19").withRun('-e "MYSQL_ROOT_PASSWORD=my-secret-pw" -p 3306:3306') { c ->
             sh 'mysql --version'
            }
        }
    }
}