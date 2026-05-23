pipeline {
agent any

environment {
    APP_NAME = "springboot-app"
    IMAGE_TAG = "v1"
}

stages {

    stage('Maven Build') {
        steps {
            sh 'mvn clean package'
        }
    }

    stage('Build & Push Docker Image') {
    steps {
        script {
            // Ensure this variable is defined globally or within the environment block if used in other stages
            IMAGE_TAG = "${BUILD_NUMBER}"
        }

        // 1. Make sure 'dockerhub-creds' matches your exact Jenkins Credentials ID
        withCredentials([usernamePassword(
            credentialsId: 'dockerhub-creds',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            // 2. Use double quotes for the sh block so Jenkins variables like ${IMAGE_TAG} expand properly
            sh """
                echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin

                docker build -t \$DOCKER_USER/springboot-app:${IMAGE_TAG} .

                docker push \$DOCKER_USER/springboot-app:${IMAGE_TAG}
            """
        }
    }
}

    stage('Update Helm Values') {
        steps {
            sh '''
               sed -i "s/tag:.*/tag: \"${BUILD_NUMBER}\"/" springboot-chart/values-dev.yaml
               '''
              }
          }
    stage('Commit GitOps Changes') {
        steps {
            sh '''
               git config --global user.email "jayaramgopi143@gmail.com"
               git config --global user.name "jayaramgopi143-code"

               git add springboot-chart/values-dev.yaml
               git commit -m "updated image tag to ${BUILD_NUMBER}"

               git push origin main
               '''
              }

          }
   }

}
