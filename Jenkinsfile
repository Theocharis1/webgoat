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
        sh "curl -v 'https://jsonplaceholder.typicode.com/posts/1'"
      }
    }
  }
}
