pipeline{
    agent{
        label "docker-ng"
    }
    stages{
        stage("Test"){
            steps{
                echo "Testing pipeline"
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