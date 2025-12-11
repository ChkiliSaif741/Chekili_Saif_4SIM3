pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // your DockerHub credentials in Jenkins
        SONAR_TOKEN = credentials('sonar-token')        // your SonarQube token in Jenkins
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
                    // Run SonarQube scan
                    sh """
                    mvn sonar:sonar \
                        -Dsonar.projectKey=springboot-app \
                        -Dsonar.projectName=springboot-app \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.token=$SONAR_TOKEN
                    """

                    // Wait for Quality Gate result (increase timeout for local Mac)
                    timeout(time: 10, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
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

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl set image deployment/spring-app spring-app=${IMAGE_NAME}:latest
                kubectl rollout status deployment/spring-app
                """
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed. Please check the logs!"
        }
        success {
            echo "Pipeline completed successfully!"
        }
    }
}
