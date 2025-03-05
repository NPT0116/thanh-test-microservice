pipeline {
    agent any

    environment {
        // Thêm Maven vào PATH cho toàn bộ pipeline
        PATH = "${env.PATH}:/opt/homebrew/Cellar/maven/3.9.9/libexec/bin"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Test') {
            steps {
                // Chạy test
                sh 'mvn test'
            }
            post {
                always {
                    // Thu thập báo cáo test
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('Build') {
            steps {
                // Build dự án
                sh 'mvn clean package'
            }
        }
    }

    post {
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
