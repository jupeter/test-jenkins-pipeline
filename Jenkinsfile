#!groovy

environment {
    ECR_URL = 'https://506212532265.dkr.ecr.eu-central-1.amazonaws.com/mestudent-registry'
    ECR_CREDENTIALS = 'ecr:eu-central-1:506212532265:repository/mestudent-registry'
}

stage('Checkout') {
    node {
        def scmVars = checkout scm

        echo "Job name: ${env.JOB_NAME} (like jupeter/test-jenkins-pipeline/master)"
        echo "Base name: ${env.JOB_BASE_NAME} (like jupeter)"

    }
}

stage('Try to push') {
    docker.withRegistry(${env.ECR_URL}, ${env.ECR_CREDENTIALS}) {
        docker.image('nginx').push('latest')
    }
}

stage('Try to setup docker container') {
    Random random = new Random()
    mysqlPort = random.nextInt(50000) + 10000
    docker.image('mysql:5.7.19').withRun("-e \"MYSQL_ROOT_PASSWORD=letsrock\" -e \"MYSQL_DATABASE=test-istudent\" -p $mysqlPort:3306") { c ->

        def ip = containerIp(c.id)
        echo "MySQL container '${c.id}', IP: ${ip}."
        sh "echo \"export MESTUDENT_TEST_DB_HOST=${ip}\" >> conf.env"
        sh "echo \"export MESTUDENT_TEST_DB_USER=root\" >> conf.env"
        sh "echo \"export MESTUDENT_TEST_DB_PASSWORD=letsrock\" >> conf.env"
        sh "echo \"export MESTUDENT_TEST_DB_PORT=3306\" >> conf.env"

        sh '. ./conf.env'

        node('ecs-java-build') {
            sh "mysql --host=${ip} --user=root --port=3306 --password=letsrock --database=test-istudent"
        }
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

def containerIp(id) {
    sh "docker inspect -f '{{ .NetworkSettings.IPAddress }}' $id > host.ip"
    readFile('host.ip').trim()
}