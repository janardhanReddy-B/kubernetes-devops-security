pipeline {
  agent any

    stages{
      stage ('Maven build')
        steps {
          sh "mvn clean package -DskipTests=true"
          archive 'target/*.jar'
        }
    }
}