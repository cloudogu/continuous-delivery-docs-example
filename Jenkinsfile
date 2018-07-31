#!groovy
@Library('github.com/cloudogu/ces-build-lib@5b32f71')
import com.cloudogu.ces.cesbuildlib.*

node('docker') {

    properties([
            // Keep only the last 10 build to preserve space
            buildDiscarder(logRotator(numToKeepStr: '10')),
            // Don't run concurrent builds for a branch, because they use the same workspace directory
            disableConcurrentBuilds()
    ])

    String defaultEmailRecipients = env.EMAIL_RECIPIENTS

    catchError {

        new Docker(this).image('node:8.11.3-alpine')
                .mountJenkinsUser()
                // Yarn calls Gulp, which uses docker images to build some PDFs. So the docker socket is needed in the container
                .mountDockerSocket()
                // Image does not contain docker client, but gulp uses it. So install it.
                .installDockerClient('17.12.1')
                .inside() {

            stage('Checkout') {
                checkout scm
            }

            stage('Install') {
                yarn 'install'
                yarn 'clean'
            }

            stage('pdf') {
                yarn 'cloudogu-pdf'
                archive 'dist/*.pdf'
            }

            stage('odt') {
                yarn 'odt'
                archive 'dist/*.odt'
            }
        }
    }

    mailIfStatusChanged(findEmailRecipients(defaultEmailRecipients))
}

void yarn(String args) {
    sh "yarn $args"
}
