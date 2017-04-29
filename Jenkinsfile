#!/usr/bin/env groovy
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('H/5 * * * *')])])

node('master') {
    git 'https://github.com/svenbs/graf-db.git'
    tag = sh(script: "git tag -l | tail -n1 > tagfile", returnStdout: true).trim()
    tag = readFile('tagfile').trim()
}

def buildDocker(String version) {
    def exec = """
        docker build ./ --pull=true --no-cache=true --force-rm -t graf-db:${version}
        docker tag graf-db:${version} \${REGISTRY}/graf-db:${version}
        docker push \${REGISTRY}/graf-db:${version}
    """

    sh exec
}

podTemplate(name: 'docker-slave') {
    node('docker-slave') {
        stage('Get a Git project') {
            deleteDir()
            cleanWs()

            checkout([$class: 'GitSCM', branches: [[name: "refs/tags/${tag}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/svenbs/graf-db']]])
            container('docker') {
                stage('Build a Docker project') {
                    buildDocker("${tag}")
                }
            }
        }
    }
}
