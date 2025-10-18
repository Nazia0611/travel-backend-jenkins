pipeline {
  agent any

  tools {
    maven 'M3'
    jdk 'Default'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps {
        sh 'mvn -B clean package -DskipTests'
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          PKG_JAR=$(ls target/*.jar | head -n1)
          pkill -f "$PKG_JAR" || true
          nohup java -jar "$PKG_JAR" --spring.profiles.active=prod > /tmp/travel-backend.log 2>&1 &
        '''
      }
    }

    stage('Test') {
      steps {
        sh 'sleep 3'
        sh 'curl -f http://localhost:8081/actuator/health || true'
      }
    }
  }
  post { failure { echo 'Backend pipeline failed' } }
}

