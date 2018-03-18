#!groovy
@Library('github.com/cloudogu/ces-build-lib@b5ce9f1')
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

        new Docker(this).image('kkarczmarczyk/node-yarn:8.0-wheezy')
                .mountJenkinsUser()
                 // Yarn calls Gulp, which uses docker images to build some PDFs. So the docker socket is needed in the container
                .mountDockerSocket()
                .inside() {

            stage('Checkout') {
                checkout scm
            }

            stage('Install') {
                // Docker socket is mounted, but the docker client is not present in this container. So install it.
                installDockerClient()
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
    sh "PATH=\$PWD/docker:\$PATH;yarn $args"
}

String installDockerClient() {
    // Installs statically linked docker binary
    sh 'wget -qc  https://download.docker.com/linux/static/stable/x86_64/docker-17.12.1-ce.tgz -O - | tar -xz'
}