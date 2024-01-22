pipeline {
    agent {
        docker {
            image 'maven:3.6.3-jdk-11'
        }
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
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'git@github.com:Enardelg/holaMundoJenkinsNexus.git']]])
            }
        }

        stage('Build Artifact') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Upload to Nexus') {
            steps {
                script {
                    pom = readMavenPom(file: 'pom.xml')
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}")
                    artifactPath = filesByGlob[0].path
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
                }
            }
        }

        stage('Build Container') {
            steps {
                sh 'docker build -t holamundo .'
                sh 'docker tag holamundo:latest 192.168.42.131:8082/holamundo:latest'
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker login -u admin -p hola1234 192.168.42.131:8082'
                sh 'docker push 192.168.42.131:8082/holamundo:latest'
            }
        }

        stage('Post') {
            steps {
                sh 'pwd'
                echo 'Clean up workfolder'
                sh 'rm -Rf *'
            }
        }
    }
}
