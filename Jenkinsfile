pipeline{
    agent{
        label "docker-ng"
    }
    stages{
        stage("Test"){
            steps{
                echo "Testing pipeline"
                send_dora_deployment(currentBuicld.result, dora-token, "https://dora.vivino.com/event-handler")
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
                    script {
                        withCredentials([string(credentialsId: 'dora-token', variable: 'dora-token')]) {
                            send_dora_deployment(currentBuicld.result, dora-token, "https://dora.vivino.com/event-handler")
                        }
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

def send_dora_deployment(String buildStatus = 'STARTED', dora_token, dora_url) {
    // build status of null means successful
    buildStatus = buildStatus ?: 'SUCCESS'

    def payload = [
        buildStatus: "${buildStatus}",
        buildUrl: "${env.BUILD_URL})",
        buildNumber: "${env.BUILD_NUMBER}",
        buildId: "${env.BUILD_ID}",
        buildName: "${env.BUILD_NAME}",
        buildStartTime: "${env.BUILD_START_TIME}",
        buildEndTime: "${env.BUILD_END_TIME}",
        buildDuration: "${env.BUILD_DURATION}",
        buildTimestamp: "${env.BUILD_TIMESTAMP}",
        buildTime: "${env.BUILD_TIME}",
        buildTag: "${env.BUILD_TAG}",
        buildBranch: "${env.BUILD_BRANCH}",
        buildUrl: "${env.BUILD_URL}",
        buildUser: "${env.BUILD_USER}",
        commitId: "${env.$GIT_COMMIT}",
        commitMessage: "${env.$GIT_MESSAGE}",
    ]

    json_payload = writeJSON text: payload, returnText: true
    sh "curl -X POST -H 'Content-Type: application/json' -H 'X-Jenkins-Token:${dora_token}' -d '${json_payload}' ${dora_url}"
}
