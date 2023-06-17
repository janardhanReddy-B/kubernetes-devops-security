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
            jacoco execPattern: 'target/jacoco.exec'
          }
        }
      }
      stage('Docker build and push') {
        steps {
          sh '$printenv'
          sh 'docker build -t bjreddy246/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push bjreddy246/numeric-app:""$GIT_COMMIT""'
        }
      }
    }
}