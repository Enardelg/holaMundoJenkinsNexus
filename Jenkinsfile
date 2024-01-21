def callBuild() {
    load 'build.groovy'
}

def callDeploy() {
    load 'deploy.groovy'
}

pipeline {
    agent none

    environment {
        // Definiciones de variables comunes para ambas pipelines

        // Maven
        MAVEN_HOME = '/opt/apache-maven-3.6.3'

        // Nexus
        NEXUS_VERSION = 'nexus3'
        NEXUS_PROTOCOL = 'http'
        NEXUS_URL = '192.168.42.131:8081'
        NEXUS_REPOSITORY = 'maven-releases'
        NEXUS_CREDENTIAL_ID = 'nexus'

        // Docker
        DOCKER_VERSION = '0.2.2'
        DOCKER_REGISTRY_URL = '192.168.42.131:8082'
        DOCKER_REGISTRY_CREDENTIAL_ID = 'docker-credentials'

        // GitHub
        GITHUB_CREDENTIAL_ID = 'github-credentials'
    }

    stages {
        stage('Build') {
            agent {
                label 'maven'
            }
            steps {
                script {
                    callBuild()
                }
            }
        }

        stage('Deploy') {
            agent {
                label 'docker'
            }
            steps {
                script {
                    callDeploy()
                }
            }
        }
    }
}
