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
    }
    post{
        always{
            dependencyCheckPublisher pattern: ''
            script{
                
            }  
        }  
    }
}
