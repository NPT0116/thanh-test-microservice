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
    def coverage = "75%"        // Ví dụ: 75% độ phủ
    def testSummary = "All tests passed"

    publishChecks context: 'Jenkins/CI',
                  conclusion: 'SUCCESS', // Sử dụng chữ in hoa
                  output: [
                      title: "Build succeeded",
                      summary: "Test result: ${testSummary}\nCoverage: ${coverage}"
                  ]
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
