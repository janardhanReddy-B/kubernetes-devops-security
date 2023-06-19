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
          withDockerRegistry([url: "", credentialsId: "docker-hub"]) {
          sh '$printenv'
          sh 'docker build -t bjreddy2468/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push bjreddy2468/numeric-app:""$GIT_COMMIT""'
          }
        }
      }

      stage('Kubernetes deployment -Dev') {
        steps {
          withKubeConfig([credentialsId: 'kubeconfig']) {
          sh "sed -i 's#replace#bjreddy2468/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
          }
        }
      }
    }
  }