pipeline {
    agent any 
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
         stage('Artifact uploader'){
            steps {
                script{
                    nexusArtifactUploader artifacts: [[artifactId: 'MyDevOpsLab', classifier: '', file: '**/*.war', type: 'war']], credentialsId: 'nexus', groupId: 'com.mydevopslab', nexusUrl: 'localhost:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'mylab-snapshot/', version: '0.0.8-SNAPSHOT'
                }
            }
         }
    }
}