pipeline {
  agent any

  tools {
    jdk 'Java17'
    maven 'Maven'
  }

  environment {
    DOCKERHUB_USER = "vannshh0007"
    IMAGE_NAME = "vannshh0007/indiaproj"
    IMAGE_TAG = "latest"
  }

  stages {

    stage('Checkout Code') {
      steps {
        echo 'Pulling from GitHub'
        git branch: 'main',
            credentialsId: 'git_cred',
            url: 'https://github.com/sakshamrastogi0005/indiaproj.git'
      }
    }

    stage('Build Project') {
      steps {
        echo 'Building Spring Boot project'
        bat 'mvn clean package -DskipTests'
      }
    }

    stage('Build Docker Image') {
      steps {
        echo 'Building Docker Image'
        bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
      }
    }

    stage('Docker Login & Push') {
      steps {
        echo 'Logging into DockerHub and pushing image'
        withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'DOCKER_PASS')]) {
          bat """
            docker login -u %DOCKERHUB_USER% -p %DOCKER_PASS%
            docker push %IMAGE_NAME%:%IMAGE_TAG%
          """
        }
      }
    }

    stage('Deploy to Minikube (Kubernetes)') {
      steps {
        echo 'Deploying to Minikube'
        bat """
          minikube status || minikube start
          minikube image load %IMAGE_NAME%:%IMAGE_TAG%
          kubectl apply -f indiaproj/deployment.yaml
          kubectl apply -f indiaproj/services.yaml
          kubectl get pods
          kubectl get svc
        """
      }
    }
  }

  post {
    success {
      echo 'BUILD + DOCKER + K8s DEPLOY SUCCESSFUL!'
    }
    failure {
      echo 'OOPS!!! PIPELINE FAILED.'
    }
  }
}


