pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        SONAR_TOKEN = credentials('sonar-token')
        IMAGE_NAME = "chkilisaif741/springboot-app"
    }

    stages {
        stage('GIT Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ChkiliSaif741/Chekili_Saif_4SIM3.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl set image deployment/spring-app spring-app=${IMAGE_NAME}:latest
                kubectl rollout status deployment/spring-app
                """
            }
        }
    }
}
