pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Lấy source code từ SCM (repo, branch) mà Jenkinsfile đang quét
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building all microservices..."
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                // Chạy test cho toàn bộ microservices
                sh "mvn test"
            }
            post {
                always {
                    // Thu thập báo cáo JUnit
                    junit '**/target/surefire-reports/*.xml'
                    
                    // Thu thập báo cáo coverage bằng JaCoCo (nếu plugin đã được cài)
                    jacoco(
                        execPattern: '**/target/jacoco.exec',
                        classPattern: '**/target/classes',
                        sourcePattern: '**/src/main/java'
                    )
                }
            }
        }

        stage('Deliver') {
            steps {
                echo "Delivering artifacts..."
                // Tùy theo cách bạn triển khai: đẩy image Docker, upload file .jar, v.v.
                // Ví dụ:
                // sh "docker build -t my-petclinic ."
                // sh "docker push my-petclinic"
            }
        }
    }
}
