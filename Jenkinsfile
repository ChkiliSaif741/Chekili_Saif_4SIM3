pipeline {
    agent any

    tools {
        maven 'MAVEN_HOME'
        jdk 'JAVA_HOME'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ChkiliSaif741/Chekili_Saif_4SIM3.git'
            }
        }

        stage('Build') {
                    steps {
                        sh 'mvn clean install -DskipTests'
                    }
        }

        stage('Package') {
                    steps {
                        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    }
                }
    }
}
