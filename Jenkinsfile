pipeline {
    agent any

    environment {
        // Thêm Maven vào PATH cho toàn bộ pipeline (đường dẫn theo cài đặt của bạn)
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
                // Chạy unit test, đảm bảo Maven đã cấu hình plugin JaCoCo trong pom.xml
                sh 'mvn test'
            }
            post {
                always {
                    // Thu thập báo cáo test
                    junit '**/target/surefire-reports/*.xml'
                    
                    // Công bố kết quả code coverage sử dụng JaCoCo plugin
                    jacoco execPattern: '**/target/jacoco.exec', 
                           classPattern: '**/target/classes', 
                           sourcePattern: '**/src/main/java', 
                           exclusionPattern: ''
                }
            }
        }
        stage('Build') {
            steps {
                // Build lại dự án
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
