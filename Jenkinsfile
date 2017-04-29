#!/usr/bin/env groovy
podTemplate(name:'docker-slave', label: 'docker-slave',
        containers: [
            containerTemplate(name: 'jnlp', image: 'jenkinsci/jnlp-slave:2.62', args: '${computer.jnlpmac} ${computer.name}'),
            containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true)
        ],
        volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]) {


    node('docker-slave') {
        stage('Get a Git project') {
            git 'https://github.com/svenbs/graf-db.git'
            container('docker') {
                stage('Build a Docker project') {
                    sh """
                    docker build --pull=true --no-cache=true -t graf-db:${tag} ./
                    docker tag graf-db:${tag} docker.svenbuesing.de/graf-db:${tag}
                    docker push docker.svenbuesing.de/graf-db:${tag}
                    """
                }
            }
        }
    }
}
