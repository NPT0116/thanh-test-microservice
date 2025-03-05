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
        stage('Build') {
            steps {
                // Chạy build đơn giản
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
