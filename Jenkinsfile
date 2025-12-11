pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
        sonarScanner 'sonar-scanner'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
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

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh """
                    sonar-scanner \
                        -Dsonar.projectKey=springboot-app \
                        -Dsonar.projectName=springboot-app \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Docker Login') {
            steps {
                sh """
                echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                """
            }
        }

        stage('Docker Push') {
            steps {
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
    }
}
