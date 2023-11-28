pipeline {
    agent any
    
    tools {
        maven "MAVEN3"
    }
    
    parameters {
        booleanParam(name: 'SKIP_TEST', defaultValue: false, description: 'Skip test stage')
        booleanParam(name: 'SKIP_DEPENDENCY_CHECK', defaultValue: false, description: 'Skip dependency-check stage')
        booleanParam(name: 'SKIP_BUG_ANALYSIS', defaultValue: false, description: 'Skip bug analysis stage')
        booleanParam(name: 'SKIP_CREDENTIAL_SCANNING', defaultValue: false, description: 'Skip bug analysis stage')
    }
    
    stages {
        
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'salary-creds', url: 'https://github.com/OT-MyGurukulam/OT-Salary.git'
            }
        }

        stage('Credentials scanning') {

            when {
                expression { params.SKIP_CREDENTIAL_SCANNING == false }
            }

            steps {
                sh 'gitleaks detect --report-path gitleaks-report.json'
            }
        }

        stage('Compile') {

            when {
                expression { params.SKIP_DEPENDENCY_CHECK == false }
            }

            steps {
                sh 'mvn package'
            }
        }

        stage('Test') {

            when {
                expression { params.SKIP_TEST == false }
            }
            
            steps {
                sh 'mvn test'
            }
        }

        stage('Dependency check') {
            
            when {
                expression { params.SKIP_DEPENDENCY_CHECK == false }
            }
            
            steps {
                script {
                    def currentWorkspace = pwd()
                    dependencyCheck additionalArguments: "--project salary --scan ${currentWorkspace} --format HTML", odcInstallation: 'DP-Check'
                }
            }
        }

        stage("Bug Analysis ") {

            when {
                expression { params.SKIP_BUG_ANALYSIS == false }
            }

            steps {
               script {
                 sh 'mvn spotbugs:spotbugs'
                 sh 'mvn site'
               }
            }
        }
        
        stage('Publish HTML Report') {
            
            steps {
                script {
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'target/site',
                        reportFiles: 'spotbugs.html',
                        reportName: 'SpotBugs Report'
                    ])
                }
            }
        }
    }
}