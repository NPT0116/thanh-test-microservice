pipeline {
    agent any

    environment {
        PATH = "C:\\Windows\\System32;${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Test') {
            steps {
                bat 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                    jacoco(
                        execPattern: '**/target/jacoco.exec',
                        classPattern: '**/target/classes',
                        sourcePattern: '**/src/main/java'
                    )
                    
                    script {
                        // Giả sử bạn đã có các giá trị số liệu sau khi xử lý báo cáo
                        def coverage = "75%"      // ví dụ: 75%
                        def testSummary = "All tests passed"  // ví dụ: tóm tắt kết quả test

                        // Sử dụng githubChecks để tạo check run trên GitHub
                        githubChecks context: 'Jenkins/CI',
                                      conclusion: 'success',
                                      output: [
                                          title: "Build succeeded",
                                          summary: "Test result: ${testSummary}\nCoverage: ${coverage}"
                                      ]
                    }
                }
            }
        }
        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }
    }

    post {
        failure {
            script {
                // Trong trường hợp build thất bại, bạn cũng có thể cập nhật thông tin
                githubChecks context: 'Jenkins/CI',
                              conclusion: 'failure',
                              output: [
                                  title: "Build failed",
                                  summary: "Build failed or coverage below threshold!"
                              ]
            }
        }
    }
}
