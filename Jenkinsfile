pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        echo "build"
      }
    }
  }
  post {
    always {
      script {
        sh """curl -X POST "http://localhost:8080/api/v2/import-scan/" -H "Authorization: Token 83f05a8624a7b22cb9bd0e5becb85b82d5e6cee2" -F "engagement=9" -F "verified=true" -F "active=true" -F "scan_date=2019-04-30" -F "scan_type=ZAP Scan" -F "minimum_severity=Info" -F "skip_duplicates=true" -F "close_old_findings=false" -F "file=@zap_report_xml.xml" """
      }
    }
  }
}
