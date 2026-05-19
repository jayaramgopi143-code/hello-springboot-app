pipeline {
agent any

environment {
    APP_NAME = "springboot-app"
    IMAGE_TAG = "v1"
}

stages {

    stage('Git Checkout') {
        steps {
            git 'https://github.com/jayaramgopi143-code/hello-springboot-app.git'
        }
    }

    stage('Maven Build') {
        steps {
            sh 'mvn clean package'
        }
    }

    stage('Docker Build') {
        steps {
            sh 'docker build -t springboot-app:v1 .'
        }
    }

    stage('Helm Deploy') {
        steps {
            sh '''
            cd /var/jenkins_home/workspace/springboot-helm-pipeline/springboot-chart

            helm upgrade --install springboot-dev . -f values-dev.yaml
            '''
        }
    }
}

}
