pipeline {

agent any

stages {

    stage('Clone Code') {
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

    stage('Load Image into Minikube') {
        steps {
            sh 'minikube image load springboot-app:v1'
        }
    }

    stage('Deploy to Kubernetes') {
        steps {
            sh 'kubectl apply -f k8s/'
        }
    }

    stage('Verify Deployment') {
        steps {
            sh 'kubectl get pods'
            sh 'kubectl get svc'
        }
    }
}

}
