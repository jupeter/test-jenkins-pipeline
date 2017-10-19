#!groovy

stage('Checkout') {
    node {
        def scmVars = checkout scm

        echo "Job name: ${env.JOB_NAME} (like jupeter/test-jenkins-pipeline/master)"
        echo "Base name: ${env.JOB_BASE_NAME} (like jupeter)"

    }
}

stage('Try to setup docker container') {
    docker.image('mysql:5').withRun('-e "MYSQL_ROOT_PASSWORD=my-secret-pw" -p 3306:3306') { c ->
        echo "no to sru"
        def ip = containerIp(c)

        echo "ip: ${ip}"
    }
}
//stage('Try to setup deploy promnt') {
//    input("Do you want to deploy?")
//}


//
//stage('Docker in docker test') {
//    node('ecs-java-build') {
//        ansiColor('xterm') {
//            try {
//                sh 'wget "https://git.io/sbt"'
//                sh 'chmod 0755 sbt'
//                sh './sbt -h'
//            } catch (e) {
//                currentBuild.result = 'FAILURE'
//                cleanWs()
//                throw e
//            }
//        }
//    }
//}

def containerIp(container) {
  sh "ip -4 addr show eth0 | grep 'inet ' | awk '{print \$2}' | awk -F '/' '{print \$1}'' > host.ip"
  readFile('host.ip').trim()
}