pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        IMAGE_NAME = "chkilisaif/springboot-app"
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
                script {
                    sh """
                    docker build -t ${IMAGE_NAME}:latest .
                    """
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    sh """
                    echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                    """
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    sh """
                    docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }
}
