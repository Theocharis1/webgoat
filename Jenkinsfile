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
                def timeStamp = Calendar.getInstance().getTime().format('YYYYMMdd-hhmmss',TimeZone.getTimeZone('CST'))
            }
            script{
                sh "curl -i -F "file=@zap_report_xml.xml" -H "Authorization: ApiKey admin:6089af38b3cc2db8d154f7724f1c9fceed3434df" -F 'scan_type=ZAP Scan' -F 'tags=apicurl' -F 'verified=true' -F 'active=true' -F scan_date=${timeStamp} -F 'engagement=/api/v2/engagements/9/' http:nginx:8000/api/v2/importscan/"
            }
        }  
    }
}
