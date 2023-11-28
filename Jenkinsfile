pipeline {
    agent any
    
    tools {
        maven "MAVEN3"
    }
    
    stages {

        stage('Compile') {

            steps {
                sh 'mvn package'
            }
        }

    }
}