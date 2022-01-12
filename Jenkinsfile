pipeline{
    agent{
        label "docker-ng"
    }
    stages{
        stage("Deploy"){
            steps{
                echo "========executing Deploy========"
            }
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========Deploy executed successfully========"
                }
                failure{
                    echo "========Deploy execution failed========"
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
            script {
              def changeLogSets = currentBuild.changeSets
              echo changeLogSets.toString()

              influxDbPublisher(
                selectedTarget: 'InfluxDB DORA',
                customData: [
                    commits: changeLogSets.toString()
                  ]
              )
            }
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}