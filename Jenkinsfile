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
      }

      stage('Mutation Tests - PIT') {
        steps {
          sh "mvn org.pitest:pitest-maven:mutationCoverage"
          }
      }

      stage('soanr Qube -SAST'){
        steps {
          withSonarQubeEnv('SonarQube') {
            sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://146.148.63.7:9000"
          }
          timeout(time: 2, unit: 'MINUTES') {
            script {
                waitForQualityGate abortPipeline: true
            }
          }
        }
      }

      stage('Vulnerability Scan - Docker ') {
            steps {
              sh "mvn dependency-check:check"
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
    post {
        always {
            junit 'target/surefire-reports/*.xml'
            jacoco execPattern: 'target/jacoco.exec'
            pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
            dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'

    }    }
  }