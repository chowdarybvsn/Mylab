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
                git branch: "master", url: "https://github.com/chowdarybvsn/Mylab.git"
              }
         }
         stage('build'){
             steps {
                mvnBuild()
             }
         }
         stage ('static code analysis'){
            steps {
                script{
                    withSonarQubeEnv('Mysonar'){
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
        stage ('Deploy to Docker'){
            steps {
                echo "Deploying ...."
                sshPublisher(publishers: 
                [sshPublisherDesc(
                    configName: 'lab_ansible_manger',
                    transfers: [
                        sshTransfer(
                            cleanRemote:false,
                            execCommand: 'ansible-playbook /opt/playbooks/deploy-to-docker.yaml -i /opt/playbooks/hosts',
                            execTimeout: 120000
                        )
                    ], 
                    usePromotionTimestamp: false, 
                    useWorkspaceInPromotion: false,
                    verbose: false)
                    ])
            }
        }
    }
}           
    
   /* sshPublisher(publishers: [sshPublisherDesc(configName: 'lab_ansible_manger', 
   transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, 
   flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], 
   usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
   */