pipeline {
    agent any 
    environment {
          ArtifactId = readMavenPom().getArtifactId()
          Version = readMavenPom().getVersion()
          Name = readMavenPom().getName()
          GroupId = readMavenPom().getGroupId()
    }
    stages {
         stage('checkout') {
              steps {
                git branch: "master", url: "https://github.com/chowdarybvsn/Mylab.git"
              }
         }
         stage('build'){
             steps {
                sh 'mvn clean install package'
             }
         }
         stage ('static code analysis'){
            steps {
                script{
                    withSonarQubeEnv(credentialsId: 'sonartoken') {
                        sh 'mvn sonar:sonar'
                  }
                }
            }
         }
        stage('print Env') {
            steps {
                echo "Artifact_ID is '${ArtifactId}'"
                echo "version is '${Version}'"
                echo "Name is '${Name}'"
                echo "GroupId is '${GroupId}'"         
             }
        }

        stage('Artifact uploader') {
            steps {
                script {
                    def NexusRepo = Version.endsWith("SNAPSHOT") ? "mylab-snapshot" : "mylab-release"

                    nexusArtifactUploader artifacts:
                    [[artifactId: "${ArtifactId}",
                    classifier: '', 
                    file: "target/${ArtifactId}-${Version}.war",
                    type: 'war']], 
                    credentialsId: 'nexus', 
                    groupId: "${GroupId}",
                    nexusUrl: 'localhost:8081', 
                    nexusVersion: 'nexus3',
                    protocol: 'http', 
                    repository: "${NexusRepo}",
                    version: "${Version}"
                }
            }
         }
    }
}