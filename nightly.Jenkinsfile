pipeline {
    agent { label "rhel8" }

    parameters {
        string(name: 'OPENSHIFT_URL_DOMAIN', defaultValue: 'openshift.com', description: 'Openshift url suffix. All other openshift url(s) will be setup according to that one')
        string(name: 'OPENSHIFT_USER', defaultValue: 'admin', description: 'Openshift user')
        string(name: 'OPENSHIFT_PASSWORD', defaultValue: '', description: 'Openshift password')
    }

    environment {
        // Keep here for documentation on the pipeline
        // Keep commented as env defined here is immutable
        // IMAGE_GEN_TAG = to be defined in `Initialize` stage
    }

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
                build(job:"./kogito-runtimes-deploy/${BRANCH_NAME}", wait: true)
            }
        }

        stage("Build & Deploy Images") {
            steps {
                // Call kogito-images-deploy
                // Temp Registry and Generated tag
                echo "Build & Deploy Images"
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