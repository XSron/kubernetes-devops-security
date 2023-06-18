pipeline {
  agent any

  stages {
      stage('Build Artifact') {
        steps {
          sh "mvn clean package -DskipTests=true"
          archive 'target/*.jar'
        }
      }   

      stage('Test') {
        steps {
          sh "mvn test"
        }
        post { //run even if "mvn test" fail
          always {
            junit 'target/surefire-reports/*.xml'
            jacoco execPattern: 'target/jacoco.exec'
          }
        }
      }

      stage('Docker build and Push') {
        steps {
          withDockerRegistry(["credentialsId": "docker-hub", "url": ""]) { //run docker login 
            sh 'printenv'
            sh 'docker build -t xsronsocheata/numeric-app:""${GIT_COMMIT}"" .'
            sh 'docker push xsronsocheata/numeric-app:""${GIT_COMMIT}""'
          }
        }
      }
    }
}