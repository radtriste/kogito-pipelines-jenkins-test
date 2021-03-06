// Should be multibranch pipeline
pipeline {
    agent { label "RHEL" }

    parameters {
        // TODO export to Jenkins credentials
        string(name: 'JENKINS_CLOUD_ROOT_URL', defaultValue: '', description: 'To access Kogito Cloud')
        string(name: 'JENKINS_CLOUD_API_TOKEN', defaultValue: '', description: 'To access Kogito Cloud')
        string(name: 'JENKINS_CLOUD_USER', defaultValue: '', description: 'To access Kogito Cloud')
        
        // TODO export to credentialsId
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
                echo "Build & Deploy Runtimes"
                // build(job:"../kogito-runtimes-deploy/${BRANCH_NAME}", wait: true)
            }
        }

        stage("Build & Deploy Images") {
            steps {
                script {
                    // Call kogito-images-deploy
                    // Temp Registry and Generated tag
                    echo "Build & Deploy Images"
                    def buildParams = [:]
                    buildParams["DEPLOY_IMAGE_REGISTRY_NAMESPACE"] = "thisisthenamespace"
                    buildParams["DEPLOY_IMAGE_TAG"] = "thisisthetag"

                    startAndWaitForRemoteBuild(params.JENKINS_CLOUD_ROOT_URL, 
                                                "tristan-pipelines-kogito-images-deploy-single",
                                                params.IMAGES_DEPLOY_REMOTE_TOKEN,
                                                params.JENKINS_CLOUD_USER, 
                                                params.JENKINS_CLOUD_API_TOKEN, 
                                                buildParams,
                                                200)
                }
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

void startAndWaitForRemoteBuild(String jenkinsUrl, String jobName, String jobToken, String jenkinsUsername, String jenkinsPassword, Map buildParams, int timeoutInSec){    
    // Get last build before to have the number so we should wait for a new one
    def previousBuildId = getRemoteJobLatestBuild(jenkinsUrl, jobName, jenkinsUsername, jenkinsPassword).id
    echo "Got previous build id ${previousBuildId}"
    
    startRemoteJobBuild(jenkinsUrl, jobName, jobToken, jenkinsUsername, jenkinsPassword, buildParams)

    timeout = 0
    while(true) {
        if (timeout > timeoutInSec){
            error "Timeout waiting for end of job ${jobName}"
        }
        
        def latestBuild = getRemoteJobLatestBuild(jenkinsUrl, jobName, jenkinsUsername, jenkinsPassword)
        echo "Got build id ${latestBuild.id}"
        if (previousBuildId != latestBuild.id){
            if(!latestBuild.building){
                if (latestBuild.result != "SUCCESS") {
                    currentBuild.result = latestBuild.result
                    error "Dependent job ${jobName} is in status ${latestBuild.result}"
                } else {
                    break
                }        
            }
        }        

        sleep(time:2, unit: "SECONDS")
        timeout += 2
    }
}

void startRemoteJobBuild(String jenkinsUrl, String jobName, String jobToken, String jenkinsUsername, String jenkinsPassword, Map buildParams=[:]){
    path = ""
    if(buildParams.size() > 0) {
        path = "buildWithParameters?token=${jobToken}"
        for(def entry : buildParams.entrySet()){
            path += "&${entry.getKey()}=${entry.getValue()}"
        }
    }else{
        path = "build?token=${jobToken}"
    }
    try {
        httpGet("${buildJobUrl(jenkinsUrl, jobName)}/${path}", jenkinsUsername, jenkinsPassword)
    }catch (e) {
        error "Error starting build for job ${jobName}: ${e.message}"
    }
}

def getRemoteJobLatestBuild(String jenkinsUrl, String jobName, String jenkinsUsername, String jenkinsPassword) {
    try {
        def callResult = httpGet("${buildJobUrl(jenkinsUrl, jobName)}/lastBuild/api/json", jenkinsUsername, jenkinsPassword)
        return readJSON(text: callResult)
    }catch (e) {
        error "Error starting build for job ${jobName}: ${e.message}"
    }
}

String buildJobUrl(String jenkinsUrl, String jobName){
    return "${jenkinsUrl}/job/${jobName}"
}

String httpGet(String url, String username, String password) {
    return httpCall(url, "GET", username, password)
}

String httpCall(String url, String method, String username, String password) {
    echo "httpCall with url ${url}"
    String auth = ""
    if (username != null && password != null) {
        auth = "-u ${username}:${password}"
    }
    httpStatus = sh (script: "curl -X ${method} -s -o curl_result -w \"%{http_code}\" ${auth} '${url}'", returnStdout: true).trim()
    echo "status = ${httpStatus}"
    output = sh(script: "cat curl_result", returnStdout: true).trim()

    if(!httpStatus.startsWith("2")){
        error "Error calling url ${url}: Return status ${httpStatus}. Output = ${output}"
    }
    return output
}