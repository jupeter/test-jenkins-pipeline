#!groovy

stage('Checkout') {
    node {
        def scmVars = checkout scm

        echo "Job name: ${env.JOB_NAME} (like jupeter/test-jenkins-pipeline/master)"
        echo "Base name: ${env.JOB_BASE_NAME} (like jupeter)"

    }
}

stage('Try to setup docker container') {
    docker.image('mysql:5.7.19').withRun('-e "MYSQL_ROOT_PASSWORD=letsrock" -e "MYSQL_DATABASE=test-istudent"') { c ->

        def ip = containerIp()
        echo "MySQL container IP: ${ip}"
        sh "echo \"export MESTUDENT_TEST_DB_HOST=${ip}\" >> conf.env"
        sh "echo \"export MESTUDENT_TEST_DB_USER=root\" >> conf.env"
        sh "echo \"export MESTUDENT_TEST_DB_PASSWORD=letsrock\" >> conf.env"

        sh 'cat ./conf.env'
        sh '. ./conf.env'

        sh "mysql --host=${ip} --user=root --port=3306 --password=letsrock --database=test-istudent"

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

def containerIp() {
    sh "ip -4 addr show eth0 | grep 'inet ' | awk '{print \$2}' | awk -F '/' '{print \$1}' > host.ip"
    readFile('host.ip').trim()
}