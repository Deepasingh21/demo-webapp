pipeline {
  agent any
  tools { 
    maven 'Maven3' // must match your Jenkins Global Tool config
    jdk 'jdk17'     // must match your Jenkins Global Tool config
  }
  environment {
    TOMCAT_URL = "http://44.222.213.109:8081/manager/text" // use /manager/text for scriptable deploy
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
          withCredentials([usernamePassword(credentialsId: 'tomcat-manager', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASS')]) {
            // Undeploy previous app (if exists)
            sh """
              curl --silent --show-error --fail -u "$TOMCAT_USER:$TOMCAT_PASS" \
                "${TOMCAT_URL}/undeploy?path=/${APP_NAME}" || true
            """
            // Deploy new WAR
            sh """
              curl --silent --show-error --fail -u "$TOMCAT_USER:$TOMCAT_PASS" \
                -T "target/${APP_NAME}.war" \
                "${TOMCAT_URL}/deploy?path=/${APP_NAME}&update=true"
            """
          }
        }
      }
    }

    stage('Smoke Test') {
      steps {
        sleep 5
        sh """
          curl -f http://44.222.213.109:8081/${APP_NAME}/ || \
          (echo "Smoke test failed" && exit 1)
        """
      }
    }
  }

  post {
    success { echo "Pipeline succeeded" }
    failure { echo "Pipeline failed" }
  }
}
