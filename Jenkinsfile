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
    environment {
        // This declares the variable safely for this stage only
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    steps {
        withCredentials([usernamePassword(
            credentialsId: '3e2de38f7d4c67c59169d20b755a6b5361de1e3fc326a8417ff690c841de8d03', // Make sure this matches your exact Jenkins Credential ID
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
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
        // Use your GitHub credentials block so Jenkins has push permissions
        withCredentials([usernamePassword(
            credentialsId: 'cfc270c2-7074-40fa-b35e-c3fb82c71021', // Your GitHub credential ID from the logs
            usernameVariable: 'GIT_USER',
            passwordVariable: 'GIT_TOKEN'
        )]) {
            sh """
                git config --global user.email "jayaramgopi143@gmail.com"
                git config --global user.name "jayaramgopi143-code"
                
                git add springboot-chart/values-dev.yaml
                git commit -m "updated image tag to ${BUILD_NUMBER}"
                
                # Setup authentication URL including the token/password securely
                git remote set-url origin https://\$GIT_USER:\$GIT_TOKEN@github.com/jayaramgopi143-code/hello-springboot-app.git
                
                # Push the current detached HEAD commit directly into the remote main branch
                git push origin HEAD:main
            """
        }
    }
}


   }

}
