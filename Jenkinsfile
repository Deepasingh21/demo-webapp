pipeline {
  agent any
  tools { maven 'Maven3' jdk 'jdk17' } // name must match Global Tool config
  environment {
    TOMCAT_URL = "http://44.222.213.109:8081/"
    APP_NAME = "demo-webapp" // context path w/o leading slash
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build & Test') {
      steps {
        sh 'mvn -B clean test package'
        archiveArtifacts artifacts: 'target/*.war', fingerprint: true
        junit 'target/surefire-reports/*.xml'
      }
    }
    stage('Deploy to Tomcat') {
      steps {
        script {
          // get credentials into env
          withCredentials([usernamePassword(credentialsId: 'tomcat-manager', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASS')]) {
            // Undeploy previous app (if exists)
            sh """
              curl --silent --show-error --fail -u "$TOMCAT_USER:$TOMCAT_PASS" \
                "${TOMCAT_URL}/undeploy?path=/${APP_NAME}" || true
            """
            // Deploy new WAR (manager/text version accepts deploy with war url or upload)
            sh """
              curl --silent --show-error --fail -u "$TOMCAT_USER:$TOMCAT_PASS" \
                -F "war=@target/${APP_NAME}.war" \
                "${TOMCAT_URL}/deploy?path=/${APP_NAME}&update=true"
            """
          }
        }
      }
    }
    stage('Smoke Test') {
      steps {
        // Wait a little for Tomcat to start the app
        sleep 5
        sh 'curl -f http://tomcat-server-host:8080/${APP_NAME}/ || (echo "Smoke test failed" && exit 1)'
      }
    }
  }
  post {
    success { echo "Pipeline succeeded" }
    failure { echo "Pipeline failed" }
  }
}
