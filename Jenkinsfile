pipeline {
    agent none

    environment {
        // Definiciones de variables comunes para ambas pipelines

        // Maven
        MAVEN_HOME = "/opt/apache-maven-3.6.3"

        // Nexus
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "192.168.42.131:8081"
        NEXUS_REPOSITORY = "maven-releases"
        NEXUS_CREDENTIAL_ID = "nexus"

        // Docker
        DOCKER_VERSION = "0.2.2"
        DOCKER_REGISTRY_URL = "192.168.42.131:8082"
        DOCKER_REGISTRY_CREDENTIAL_ID = "docker-credentials"

        // GitHub
        GITHUB_CREDENTIAL_ID = "github-credentials"
    }

    stages {
        stage('Build') {
            agent {
                label 'maven'
            }
            steps {
                // Contenido de build.groovy
                script {
                    // Contenido de build.groovy
                    sh '''
                        #mvn clean install
                        /opt/apache-maven-3.6.3/bin/mvn package
                    '''
                }
            }
        }

        stage('Deploy') {
            agent {
                label 'docker'
            }
            steps {
                // Contenido de deploy.groovy
                script {
                    // Contenido de deploy.groovy
                    sh '''
                        pwd 
                        curl -v -u admin:hola1234 -o app.jar http://192.168.42.131:8081/repository/maven-releases/org/springframework/Jenkins-holamundo/0.2.2/Jenkins-holamundo-0.2.2.jar
                    '''
                }
            }
        }

        // Otras etapas pueden agregarse según sea necesario

        stage("Post") {
            agent {
                label 'docker'
            }
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
