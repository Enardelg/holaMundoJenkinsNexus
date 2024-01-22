pipeline {
    agent {
        label 'maven'
    }

    environment {
        NEXUS_VERSION = 'nexus3'
        NEXUS_PROTOCOL = 'http'
        NEXUS_URL = '192.168.42.131:8081'
        NEXUS_REPOSITORY = 'maven-releases'
        NEXUS_CREDENTIAL_ID = 'nexus'
        DOCKER_VERSION = '0.2.2'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'git@github.com:calamza/holamundo.git']]])
            }
        }

        stage('Build Artifact') {
            steps {
                sh '''
          mvn clean install
        '''
            }
        }

        stage('Upload to Nexus') {
            steps {
                script {
                    pom = readMavenPom(file: 'pom.xml')
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}")
                    artifactPath = filesByGlob[0].path
                    artifactExists = fileExists artifactPath

                    if (artifactExists) {
                        nexusArtifactUploader(
              nexusVersion: NEXUS_VERSION,
              protocol: NEXUS_PROTOCOL,
              nexusUrl: NEXUS_URL,
              groupId: pom.groupId,
              version: pom.version,
              repository: NEXUS_REPOSITORY,
              credentialsId: NEXUS_CREDENTIAL_ID,
              artifacts: [
                [artifactId: pom.artifactId,
                 classifier: '',
                 file: artifactPath,
                 type: pom.packaging],

                [artifactId: pom.artifactId,
                 classifier: '',
                 file: 'pom.xml',
                 type: 'pom']
              ]
            )
          } else {
                        error "*** File: ${artifactPath}, could not be found"
                    }
                }
            }
        }

        stage('Post') {
            steps {
                sh '''
          pwd
          echo "Clean up workfolder"
          rm -Rf *
        '''
            }
        }
    }
}
