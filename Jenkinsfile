#!groovy

extWorkspace = exwsAllocate 'diskpool1'


ecr = [
        url: 'https://506212532265.dkr.ecr.eu-central-1.amazonaws.com',
        credentials: 'ecr:eu-central-1:ecr-credentials'
]

def mainScmGitCommit = null

// stage('Checkout') {
//     node('ecs-java') {
//         docker.withRegistry(ecr['url'], ecr['credentials']) {
//             docker.image('mestudent').push('7bf1b84279abdb63924d20fd97d057d5bcf331cd')
//         }
//     }
// }
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
// stage('Try to setup deploy promnt') {
//     input("Do you want to deploy?")
// }


stage('Try to get username') {
    node {
        checkout scm

        def changeLogSets = currentBuild.changeSets
for (int i = 0; i < changeLogSets.size(); i++) {
    def entries = changeLogSets[i].items
    for (int j = 0; j < entries.length; j++) {
        def entry = entries[j]
        echo "${entry.commitId} by ${entry.author} on ${new Date(entry.timestamp)}: ${entry.msg}"
        def files = new ArrayList(entry.affectedFiles)
        for (int k = 0; k < files.size(); k++) {
            def file = files[k]
            echo "  ${file.editType.name} ${file.path}"
        }
    }
}
    }

}

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
    // def build = currentBuild.rawBuild
    // def cause = build.getCause(hudson.model.Cause.UserIdCause.class)
    // if (cause != null) {
    //     // manual build
    //     return cause.getUserId()
    // }

    def SCMCause  = currentBuild.rawBuild.getCause(hudson.triggers.SCMTrigger$SCMTriggerCause)

    if (SCMCause != null) {
        // git automatic build
        println SCMCause.properties

        //return cause.getUserId()
    }
    
    return null
}