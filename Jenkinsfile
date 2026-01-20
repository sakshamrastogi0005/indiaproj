pipeline {
    agent any

    tools {
        jdk 'Java17'
        maven 'Maven'
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

        stage('Test Code') {
            steps {
                echo 'JUnit test execution started'
                bat 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                    echo 'Test execution completed'
                }
            }
        }

        stage('Build Project') {
            steps {
                echo 'Building Java project'
                bat 'mvn package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image'
                bat 'docker build -t myindiaproj:1.0 .'
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                echo 'Pushing Docker image to DockerHub'
                withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'DOCKER_PASS')]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u vannshh0007 --password-stdin
                    docker tag myindiaproj:1.0 vannshh0007/myindiaproj:1.0
                    docker push vannshh0007/myindiaproj:1.0
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'BUILD AND DOCKER PUSH SUCCESSFUL'
        }
        failure {
            echo 'PIPELINE FAILED'
        }
    }
}


