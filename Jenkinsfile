pipeline {
    agent any
    
    tools {
        maven 'maven3'
        jdk 'openjdk-11'
    }
    
    stages {
        stage('Install') {
            steps {
                sh "mvn clean install -DskipTests"
            }
        }
        stage('Sonar') {
            steps {
                sh "mvn sonar:sonar -Dsonar.host.url=${"http://sonarqube:9000"}"
            }
        }
        stage('Dependency-Check') {
            steps {
                dependencyCheck additionalArguments: '-format HTML', odcInstallation: 'Dependency-Check-5.3.2'
            }
        }
        
    stage('Import context file to ZAP') {
            steps {
                script {
                    try {
                        // Context import fails if it already exists
                        sh 'zap-cli --zap-url zap -p 8000 --api-key 5364864132243598723485 --port 8000 context import /zap/data/WebGoat.context'
                        }
                    catch (Exception e) {
                    }
                 }
            }
        }
    }
    post{
        always{
            dependencyCheckPublisher pattern: ''
            script{
                def timeStamp = Calendar.getInstance().getTime().format('YYYYMMdd-hhmmss',TimeZone.getTimeZone('CST'))
            }
        }
    }
}
