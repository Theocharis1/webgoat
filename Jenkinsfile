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

        stage('Scan staging environment with ZAP') {
            steps {
                script {
                    try {
                        // If it finds results, returns error code, but we still want to publish the report
                        sh 'zap-cli --zap-url zap -p 8000 --api-key 5364864132243598723485 quick-scan -c WebGoat -u tester -s all --spider -r http://webgoat:8085/WebGoat'
                        }
                    catch (Exception e) {
                    }
                 }
            }
        }
        
        stage('Generate ZAP Report') {
            steps {
                sh 'zap-cli --zap-url zap -p 8000 --api-key 5364864132243598723485 report -o zap_report_xml.xml -f xml'
            }
        }    
    }
    post{
        always{
            publishHTML([
                allowMissing: false, 
                alwaysLinkToLastBuild: false, 
                keepAll: false, 
                reportDir: '', 
                reportFiles: 'zap_report.xml', 
                reportName: 'ZAP DAST Report', 
                reportTitles: ''
            ])
            dependencyCheckPublisher pattern: ''
            
            script{
                final String currentTime = sh(returnStdout: true, script: 'date +%Y-%m-%d').trim()
                sh """curl -X POST "http://nginx:8080/api/v2/import-scan/" -H "Authorization: Token 83f05a8624a7b22cb9bd0e5becb85b82d5e6cee2" -F "engagement=9" -F "verified=true" -F "active=true" -F "scan_date=$currentTime" -F "scan_type=ZAP Scan" -F "minimum_severity=Info" -F "skip_duplicates=true" -F "close_old_findings=false" -F "file=@zap_report_xml.xml" """
            }
            
        }
    }
}
