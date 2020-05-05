// @Library('jenkins-pipeline-shared-libraries')_

pipeline {
    agent {
        label 'rhel-8'
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
                    echo "maven.runMavenWithSubmarineSettings('clean deploy', false)"
                }
            }
        }
        stage('Build kogito-apps') {
            steps {
                echo """
                    checkout([$class: 'GitSCM', branches: [[name: env.BRANCH_NAME]], browser: [$class: 'GithubWeb', repoUrl: 'git@github.com:kiegroup/kogito-apps.git'], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'kogito-apps']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'kie-ci-user-key', url: 'git@github.com:kiegroup/kogito-apps.git']]])
                    dir("kogito-apps") {
                        script {
                        maven.runMavenWithSubmarineSettings('clean deploy', false)
                        }
                    }
                """
            }
        }
        stage('Build kogito-examples') {
            steps {
                echo """
                    checkout([$class: 'GitSCM', branches: [[name: env.BRANCH_NAME]], browser: [$class: 'GithubWeb', repoUrl: 'git@github.com:kiegroup/kogito-examples.git'], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'kogito-examples']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'kie-ci-user-key', url: 'git@github.com:kiegroup/kogito-examples.git']]])
                    dir("kogito-examples") {
                        script {
                            maven.runMavenWithSubmarineSettings('clean deploy', false)
                        }
                    }
                """
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
            junit '**/target/surefire-reports/**/*.xml'
            cleanWs()
        }
    }
}
