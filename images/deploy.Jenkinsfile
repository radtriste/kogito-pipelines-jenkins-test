//Checks master branch for updates every day
//If found triggers the pipeline, 
//The pipeline is set to run between 12:00AM - 3:00AM UTC

pipeline{
    agent { label 'jenkins-slave'}
    //  triggers{
    //     pollSCM(
    //         scmpoll_spec: '@midnight', 
    //         ignorePostCommitHooks: true
    //     )
    // }
    stages{
        stage('Initialization'){
            steps{
                echo "Initialization"
            }
        }
        stage('Prepare offline kogito-examples'){
            steps{
                echo "Prepare offline kogito-examples"
            }
        }
        stage('Build and test kogito-quarkus-ubi8 image'){
            steps{
                echo "Build and test kogito-quarkus-ubi8 image"
            }
        }
        stage('Build and test kogito-quarkus-jvm-ubi8 image'){
            steps{
                echo "Build and test kogito-quarkus-jvm-ubi8 image"
            }
        }
        stage('Build and test kogito-quarkus-ubi8-s2i image'){
            steps{
                echo "Build and test kogito-quarkus-ubi8-s2i image"
            }
        }
        stage('Build and test kogito-springboot-ubi8 image'){
            steps{
                echo "Build and test kogito-springboot-ubi8 image"
            }
        }
        stage('Build and test kogito-springboot-ubi8-s2i image '){
            steps{
                echo "Build and test kogito-springboot-ubi8-s2i image"
            }
        }
        stage('Build and test kogito-data-index image '){
            steps{
                echo "Build and test kogito-data-index image"
            }
        }
        stage('Build and test kogito-jobs-service image '){
            steps{
                echo "Build and test kogito-jobs-service image"
            }
        }
        stage('Build and test kogito-management-console image '){
            steps{
                echo "Build and test kogito-management-console image"
            }
        }
        stage('Tagging'){
            steps{
                echo "Tagging"
            }
        }
        stage('Pushing'){
            steps{
                echo "Pushing"
            }
        }
        stage('Finishing'){
            steps{
                echo "Finishing"
            }
        }
    }
    post{
        failure {
            echo "send failure message"
        }
        unstable {
            echo "send unstable message"
        }
        always {
            //junit 'target/test/results/*.xml'
            echo "Get junit files"
        }
    }
}
