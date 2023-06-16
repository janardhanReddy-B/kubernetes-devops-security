pipeline {
  agent any

    stages {
      stage('Maven build') {
        steps {
          sh "mvn clean package -DskipTests=true"
          archive 'target/*.jar'
        }
      }
      stage('Unit Tests and Jacoco') {
        steps {
          sh "mvn test"
        }
        post{
          always {
            junit 'target/surefire-reports/*.xml'
            jacoco-execPattern: 'target/jacoco.exec'
          }
        }
      }

    }
}