// Should be multibranch pipeline
pipeline {
    agent { label "RHEL" }

    parameters {
        string(name: 'JENKINS_CLOUD_ROOT_URL', defaultValue: '', description: 'To access Kogito Cloud')
        string(name: 'JENKINS_CLOUD_API_TOKEN', defaultValue: '', description: 'To access Kogito Cloud')
        string(name: 'JENKINS_CLOUD_USER', defaultValue: '', description: 'To access Kogito Cloud')
        
        string(name: 'IMAGES_DEPLOY_REMOTE_TOKEN', defaultValue: '', description: 'To access Kogito Cloud')
        //string(name: 'OPERATOR_DEPLOY_REMOTE_TOKEN', defaultValue: '', description: 'To access Kogito Cloud')

        string(name: 'OPENSHIFT_URL_DOMAIN', defaultValue: 'openshift.com', description: 'Openshift url suffix. All other openshift url(s) will be setup according to that one')
        string(name: 'OPENSHIFT_USER', defaultValue: 'admin', description: 'Openshift user')
        string(name: 'OPENSHIFT_PASSWORD', defaultValue: '', description: 'Openshift password')
    }

    // environment {
        // Keep here for documentation on the pipeline
        // Keep commented as env defined here is immutable
        // IMAGE_GEN_TAG = to be defined in `Initialize` stage
    // }

    stages {
        stage("Initialize") {
            steps {
                script {
                    env.IMAGE_GEN_TAG = "Nightly-Build-${BUILD_NUMBER}"
                }
            }
        }

        stage("Build & Deploy Runtimes") {
            steps {
                // Call kogito-runtimes-deploy
                build(job:"../kogito-runtimes-deploy/${BRANCH_NAME}", wait: true)
            }
        }

        stage("Build & Deploy Images") {
            steps {
                // Call kogito-images-deploy
                // Temp Registry and Generated tag
                echo "Build & Deploy Images"
                startAndWaitForRemoteBuild("tristan-pipelines-kogito-images-deploy-single", 200)
            }
        }

        stage("Build & Deploy Operator") {
            steps {
                // Call kogito-images-deploy
                // Temp Registry and Generated tag
                echo "Build & Deploy Operator"
            }
        }

        stage("Promote images") {
            steps {
                // Call kogito-images-promote
                // Old = Temp registry and Generated tag
                // New = Nightly registry & nightly-{date} as tag
                echo "Promote Images"
            }
        }

        stage("Promote operator image") {
            steps {
                // Call kogito-images-promote
                // Old = Temp registry and Generated tag
                // New = Nightly registry & nightly-{date} as tag
                echo "Promote Operator"
            }
        }
    }
    post {
        failure {
            // Send message here
            echo "Send failing message"
        }
        unstable {
            // Send message here
            echo "Send unstable message"
        }
    }
}

void startAndWaitForRemoteBuild(String jobName, int timeoutInSec){
    String url = "${params.JENKINS_CLOUD_ROOT_URL}/job/${jobName}"
    def exitCode = sh (script:"curl -X POST \"${JENKINS_CLOUD_USER}:${JENKINS_CLOUD_API_TOKEN}\" ${url}/build?token=${IMAGES_DEPLOY_REMOTE_TOKEN}", returnStatus: true)
    if (exitCode != 0) {
        error "Error starting build for job ${jobName}"
    }

    timeout = 0
    while(true) {
        if (timeout > timeoutInSec){
            error "Timeout waiting for end of job ${jobName}"
        }
        sleep(time:2, unit: "SECONDS")
        timeout += 2
        
        exitCode = sh (script:"curl -X POST \"${JENKINS_CLOUD_USER}:${JENKINS_CLOUD_API_TOKEN}\" ${url}/lastBuild/api/json > curl_result", returnStatus: true)
        if (exitCode != 0) {
            error "Error getting latest build for job ${jobName}"
        }
        def status = readJson file: "curl_result"
        if (status.result != "SUCCESS") {
            currentBuild.result = status.result
            error "Dependent job ${jobName} is in status ${status.result}"
        } else {
            break
        }
    }
}