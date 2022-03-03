pipeline{
    agent{
        label "docker-ng"
    }
    stages{
        stage("Test"){
            steps{
                echo "Testing pipeline"
                script {
                    for (comment in pullRequest.comments) {
                        echo "Author: ${comment.user}, Comment: ${comment.body}"
                        if (comment.body.contains("awesome")) {
                            pullRequest.editComment(comment.id, "this is a very edited comment")
                        }
                    }
                }
            }
        }
        stage("Deploy"){
            when {
                branch "main"
            }
            steps{
                echo "deploying to production"
            }
            post{
                success{
                    echo "notifying DORA of successful deployment"
                    withCredentials([string(credentialsId: 'dora-token', variable: 'doraToken')]) {
                        send_dora_deployment(currentBuild.result, doraToken, "https://dora.vivino.com/event-handler")
                    }
                }
                unsuccessful{
                    echo "notifying DORA of unsuccessful deployment"
                }
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
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
