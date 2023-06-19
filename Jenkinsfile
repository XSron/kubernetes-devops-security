pipeline {
  agent any

  stages {
      stage('Build Artifact') {
        steps {
          sh "mvn clean package -DskipTests=true"
          archive 'target/*.jar'
        }
      }   

      stage('Unit Test and Coverage Test') {
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

      // stage('Deploy and Run') {
      //   steps {
      //     sh 'docker run xsronsocheata/numeric-app:""${GIT_COMMIT}""'
      //   }
      // }

      stage('Deploy to Kubernetes - DEV') {
        steps {
          withKubeConfig(['credentialsId': 'kubeconfig']) { //login to kube api server
              sh "sed -i 's#replace#xsronsocheata/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml" //replace container name with the current commit
              sh "kubectl apply -f k8s_deployment_service.yaml"
          }
        }
      }
     }
}