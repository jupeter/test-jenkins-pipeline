#!groovy

stage('Checkout') {
    node {
        def scmVars = checkout scm

        echo "Job name: ${env.JOB_NAME} (like jupeter/test-jenkins-pipeline/master)"
        echo "Base name: ${env.JOB_BASE_NAME} (like jupeter)"

    }
}

stage('Very important questions') {
    input("Do you want to deploy?")
}

stage('Docker in docker test') {
    node('ecs-java-build') {
        ansiColor('xterm') {
            try {
                sh 'wget "https://git.io/sbt"'
                sh 'chmod 0755 sbt'
                sh './sbt -h'
            } catch (e) {
                currentBuild.result = 'FAILURE'
                cleanWs()
                throw e
            }
        }
    }
}
