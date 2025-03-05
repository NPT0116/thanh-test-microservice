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
                    // Thu thập kết quả test
                    junit '**/target/surefire-reports/*.xml'
                    // Thu thập báo cáo độ phủ với JaCoCo
                    jacoco(
                        execPattern: '**/target/jacoco.exec',
                        classPattern: '**/target/classes',
                        sourcePattern: '**/src/main/java'
                    )
                    script {
                        // Ví dụ: trích xuất số liệu độ phủ và tóm tắt kết quả test
                        def coverage = "75%"         // Ví dụ: 75% độ phủ
                        def testSummary = "All tests passed"

                        // Sử dụng publishChecks để cập nhật thông tin lên GitHub
                        publishChecks context: 'Jenkins/CI',
                                      conclusion: 'SUCCESS',
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
                // Trong trường hợp build thất bại, cập nhật trạng thái FAILURE
                publishChecks context: 'Jenkins/CI',
                              conclusion: 'FAILURE',
                              output: [
                                  title: "Build failed",
                                  summary: "Build failed or coverage below threshold!"
                              ]
            }
        }
    }
}
