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
    }
}
