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
            influxDbPublisher selectedTarget: 'InfluxDB DORA'
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}