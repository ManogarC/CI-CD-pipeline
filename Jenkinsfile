
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Tools') {
            steps {
                bat 'java -version'
                bat 'mvn -version'
                bat 'git --version'
                bat 'docker --version'
            }
        }

        stage('Compile') {
            steps {
                bat 'mvn -B clean compile'
            }
        }

        stage('Unit Tests') {
            steps {
                bat 'mvn -B test'
            }
        }

        stage('Package JAR') {
            steps {
                bat 'mvn -B package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t jenkins-java-demo:%BUILD_NUMBER% .'
            }
        }

        stage('Docker Smoke Test') {
            steps {
                bat 'docker run --rm jenkins-java-demo:%BUILD_NUMBER%'
            }
        }
    }

    post {
        always {
            junit testResults: 'target/surefire-reports/TEST-*.xml',
                  allowEmptyResults: true
        }

        success {
            archiveArtifacts artifacts: 'target/*.jar',
                             fingerprint: true
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
