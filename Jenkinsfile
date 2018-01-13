#!groovy

extWorkspace = exwsAllocate 'diskpool1'


ecr = [
        url: 'https://506212532265.dkr.ecr.eu-central-1.amazonaws.com',
        credentials: 'ecr:eu-central-1:ecr-credentials'
]

def mainScmGitCommit = null

stage('Checkout') {
    node {
        checkout scm
    }
}

//
//stage('Try to setup docker container') {
//    Random random = new Random()
//    mysqlPort = random.nextInt(50000) + 10000
//    docker.image('mysql:5.7.19').withRun("-e \"MYSQL_ROOT_PASSWORD=letsrock\" -e \"MYSQL_DATABASE=test-istudent\" -p $mysqlPort:3306") { c ->
//
//        def ip = containerIp(c.id)
//        echo "MySQL container '${c.id}', IP: ${ip}."
//        sh "echo \"export MESTUDENT_TEST_DB_HOST=${ip}\" >> conf.env"
//        sh "echo \"export MESTUDENT_TEST_DB_USER=root\" >> conf.env"
//        sh "echo \"export MESTUDENT_TEST_DB_PASSWORD=letsrock\" >> conf.env"
//        sh "echo \"export MESTUDENT_TEST_DB_PORT=3306\" >> conf.env"
//
//        sh '. ./conf.env'
//
//        node('ecs-java-build') {
//            sh "mysql --host=${ip} --user=root --port=3306 --password=letsrock --database=test-istudent"
//        }
//    }
//}

stage('Try to setup deploy promnt') {
    try {
        timeout(time: 1, unit: 'HOURS') {
            def feedback = input(message: 'Should we proceed', submitter: "MeStudent*Developers", submitterParameter: 'approver', parameters:[booleanParam(defaultValue: false, description: '', name: 'promote')] )

            echo "Verification passed (accepted by: ${feedback.approver})"
        }
    } catch(err) {
        echo "Missing approve/reject before timeout or error."
    }
}


//stage('Try to get username') {
//    node {
//        checkout scm
//
//        def user = getUsername()
//
//        echo "PR User test: ${user}"
//
//        echo "Test2: ${env.CHANGE_AUTHOR}"
//        echo "CHange target: ${env.CHANGE_TARGET}"
//    }
//}
//
//stage('Status try123') {
//    node {
//        context="context123"
//        setBuildStatus(context, 'Start trying failure...', 'PENDING')
//        setBuildStatus(context, 'Checked and fail', 'FAILURE')
//
//        context="context2344"
//        setBuildStatus(context, 'Start trying passing...', 'PENDING')
//        setBuildStatus(context, 'Checked and pass', 'SUCCESS')
//
//    }
//}
//
//void setBuildStatus(context, message, state) {
//    // partially hard coded URL because of https://issues.jenkins-ci.org/browse/JENKINS-36961, adjust to your own GitHub instance
//    step([
//            $class: "GitHubCommitStatusSetter",
//            contextSource: [$class: "ManuallyEnteredCommitContextSource", context: context],
//            statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
//    ]);
//}


//stage('Try to cache node') {
//    node {
//        def baseName = getProjectName()
//        def reference = "/mnt/cluster/node-reference/${baseName}"
//        Random random = new Random()
//        tempDir = random.nextInt(50000) + 10000
//
//        sh 'mkdir node_modules || true'
//        sh 'touch node_modules/sample_file2 || true'
//
//        // check and use cache
//        echo "Check if we got cache and re-use it"
//        sh "if [ -d $reference/node_modules ]; then rm -R ./node_modules; cp -R $reference/node_modules .; fi"
//
//        // cache
//        echo "Cache last build"
//        sh """
//            mkdir -p $reference
//            cp -R node_modules $reference/node_modules.$tempDir
//            rm -R $reference/node_modules || true
//            mv $reference/node_modules.$tempDir $reference/node_modules
//        """
//        sh ""
//    }
//}

//
//stage('Get checkout in container') {
//    node('ecs-java') {
//        ansiColor('xterm') {
//            exws (extWorkspace) {
//
//                def commitHash = mainScmGitCommit
//                echo "Commit: $commitHash"
//            }
//        }
//    }
//}
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
//
//def containerIp(id) {
//    sh "docker inspect -f '{{ .NetworkSettings.IPAddress }}' $id > host.ip"
//    readFile('host.ip').trim()
//}

def getProjectName() {
    return env.JOB_NAME.substring(0, env.JOB_NAME.indexOf("/"))
}

def getScmUrl() {
    def userConfig = scm.userRemoteConfigs
    return userConfig[0].url
}

def getScmCommitHash() {
    mainScmGitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    return mainScmGitCommit
}

def scmCheckout() {
    def baseName = getProjectName()
    def reference = "/mnt/cluster/git-reference/${baseName}/test-jenkins-pipeline.git"

    echo "Repository url: ${url}"
    echo "Cache reference: ${reference}"

    checkout([
            $class: 'GitSCM',
            branches: scm.branches,
            extensions: scm.extensions + [[
                                                  $class: 'CloneOption',
                                                  depth: 0,
                                                  noTags: true,
                                                  reference: "$reference"
                                          ]],
            userRemoteConfigs: scm.userRemoteConfigs
    ])
}


def getUsername() {
    def build = currentBuild.rawBuild
    def cause = build.getCause(hudson.model.Cause.UserIdCause.class)
    if (cause != null) {
        // manual build
        return cause.getUserId()
    }

    def changeAuthors = currentBuild.changeSets.collect { set ->
        set.collect { entry -> entry.author.id }
    }.flatten()

    if(changeAuthors.size == 0) {
        return null
    }

    return changeAuthors[0]
}