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
                reportFiles: 'zap_report.html', 
                reportName: 'ZAP DAST Report', 
                reportTitles: ''
            ])
            dependencyCheckPublisher pattern: ''
            
            time=$(date +'%Y-%m-%d')
            curl -i -F "file=@zap_report_xml.xml" -H "Authorization: ApiKey admin:6089af38b3cc2db8d154f7724f1c9fceed3434df" -F 'scan_type=ZAP Scan' -F 'tags=apicurl' -F 'verified=true' -F 'active=true' -F scan_date=${time} -F 'engagement=/api/v2/engagements/9/' http://127.0.0.1:8000/api/v2/importscan/
        }
    }
}
