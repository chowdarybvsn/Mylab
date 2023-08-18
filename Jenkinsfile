@Library("my-sh-lib") _
pipeline {
    agent any 
    environment {
          ArtifactId = readMavenPom().getArtifactId()
          Version = readMavenPom().getVersion()
          Name = readMavenPom().getName()
          GroupId = readMavenPom().getGroupId()
    }
    stages {
         stage('checkout'){
              steps {
                checkout()
              }
         }
         stage('hello world'){
            steps {
                helloWorld()
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
                    scA()
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