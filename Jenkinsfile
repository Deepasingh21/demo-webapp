pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
      post { always { archiveArtifacts artifacts: 'target/*.war', fingerprint: true } }
    }
    stage('Test') {
      steps {
        sh 'mvn test -B'
      }
    }
  }
  post {
    success { echo 'Build succeeded' }
    failure { echo 'Build failed' }
  }
}
