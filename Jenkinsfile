@Library('pipeline-library-misc@jenkins-libs/fix/branch_indexing') _
timestamps {
    buildRepo([
        "stagingEnv": "",
        "releaseTag": [
            baseBranch: "main",
            environment: "testing"
        ],
        "buildArguments": [
            "ENVIRONMENT": "testing"
        ]
    ])
}


def send_dora_deployment(String buildStatus = 'STARTED', doraToken, doraUrl) {
    // build status of null means successful
    buildStatus = buildStatus ?: 'SUCCESS'
    echo "${env.GIT_COMMIT}"
    echo "${env.GIT_BRANCH}"
    echo "${env.CHANGE_AUTHOR}"
    buildTimestamp = new Date().format('yyyy-MM-dd HH:mm:ss', TimeZone.getTimeZone('UTC'))

    payload = [
        projectName: "test-service",
        buildStatus: "${buildStatus}",
        buildUrl: "${env.BUILD_URL})",
        buildNumber: "${env.BUILD_NUMBER}",
        buildDuration: currentBuild.duration,
        buildTimestamp: buildTimestamp,
        buildTag: "${env.BUILD_TAG}",
        buildBranch: "${env.GIT_BRANCH}",
        buildUrl: "${env.BUILD_URL}",
        changeAuthor: "${env.CHANGE_AUTHOR}",
        commitId: "${env.GIT_COMMIT}",
        commitMessage: "${env.CHANGE_TITLE}",
        buildCauses: currentBuild.getBuildCauses().toString()
    ]

    echo payload.toString()

    signature = UUID.randomUUID().toString()
    json_payload = writeJSON json: payload, returnText: true
    sh('curl -X POST -H "Content-Type: application/json" -H "X-Jenkins-Token: $doraToken" ' + "-H 'X-Jenkins-Signature: ${signature}' -d '${json_payload}' ${doraUrl}")
}
