pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
      stage('unit test mvn and jacoco') {
             steps {
                sh "mvn test"
             post{
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
             }
      stage ('Docker build and push') {
        steps {
          withDockerRegistery([credentialsId: "docker-hub", url: ""]) {
            sh 'printenv'
            sh 'docker build -t bjreddy2468/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker run bjreddy2468/numeric-app:""$GIT_COMMIT""'
          }
        }
      }

    }
}