// @Library('jenkins-pipeline-shared-libraries')_

pipeline {
    agent {
        label 'RHEL7'
    }
    // environment {
    //     KOGITO_CI_EMAIL_TO = credentials('KOGITO_CI_EMAIL_TO')
    // }
    // tools {
    //     maven 'kie-maven-3.6.2'
    //     jdk 'kie-jdk11'
    // }
    // triggers {
    //     cron ('H 9 * * *')
    // }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')
        timeout(time: 90, unit: 'MINUTES')
    }
    stages {
        stage('Initialize') {
            steps {
                sh 'printenv'
            }
        }
        stage('Build kogito-runtimes') {
            steps {
                script {
                    echo "Test & Deploy runtimes"
                }
            }
        }
        stage('Build kogito-apps') {
            steps {
                echo "Checkout & Test & Deploy apps"
            }
        }
        stage('Build kogito-examples') {
            steps {
                echo "Checkout & Test & Deploy examples"
            }
        }
    }
    post {
        failure {
            echo "send failure message"
        }
        unstable {
            echo "send unstable message"
        }
        always {
            //junit '**/target/surefire-reports/**/*.xml'
            cleanWs()
        }
    }
}
