pipeline {
    agent any 
    stages {
         stage('checkout') {
              steps {
                git branch:"master", url = "https://github.com/chowdarybvsn/Mylab.git"
              }
         }
         stage('build'){
             steps {
                sh 'mvn clean install package'
             }
         }
    }
}