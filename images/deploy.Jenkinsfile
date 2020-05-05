pipeline {
    agent { label "rhel8" }

    parameters {
        // TODO
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
                checkout([$class: 'GitSCM', branches: [[name: env.BRANCH_NAME]], browser: [$class: 'GithubWeb', repoUrl: 'git@github.com:kiegroup/kogito-apps.git'], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'kogito-apps']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'kie-ci-user-key', url: 'git@github.com:kiegroup/kogito-apps.git']]])
                dir("kogito-apps") {
                    script {
                      maven.runMavenWithSubmarineSettings('clean deploy', false)
                    }
                }
            }
        }
        stage('Build kogito-examples') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: env.BRANCH_NAME]], browser: [$class: 'GithubWeb', repoUrl: 'git@github.com:kiegroup/kogito-examples.git'], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'kogito-examples']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'kie-ci-user-key', url: 'git@github.com:kiegroup/kogito-examples.git']]])
                dir("kogito-examples") {
                    script {
                         maven.runMavenWithSubmarineSettings('clean deploy', false)
                    }
                }
            }
        }
    }
    post {
        failure {
            // send message
        }
        unstable {
            // send message
        }
        always {
            junit '**/target/surefire-reports/**/*.xml'
            cleanWs()
        }
    }
}