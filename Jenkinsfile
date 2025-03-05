pipeline {
    agent any

    environment {
        // Bổ sung đường dẫn Maven vào PATH (tuỳ máy)
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
                    
                    // Publish coverage nếu có JaCoCo
                    jacoco(
                        execPattern: '**/target/jacoco.exec',
                        classPattern: '**/target/classes',
                        sourcePattern: '**/src/main/java'
                    )
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }

    post {
        success {
            // Cập nhật status = SUCCESS lên GitHub
            script {
                step([
                    $class: 'GitHubCommitStatusSetter',
                    reposSource: [
                        $class: 'ManuallyEnteredRepositorySource',
                        url: 'https://github.com/NPT0116/thanh-test-microservice.git' // Sửa thành repo của bạn
                    ],
                    contextSource: [
                        $class: 'ManuallyEnteredCommitContextSource',
                        context: 'Jenkins/CI'  // Tên hiển thị trong status, tuỳ chọn
                    ],
                    statusResultSource: [
                        $class: 'ConditionalStatusResultSource',
                        results: [
                            [
                                $class: 'AnyBuildResult',
                                state: 'SUCCESS',
                                message: 'All builds passed!'
                            ]
                        ]
                    ]
                ])
            }
            echo 'Build succeeded!'
        }
        failure {
            // Cập nhật status = FAILURE lên GitHub
            script {
                step([
                    $class: 'GitHubCommitStatusSetter',
                    reposSource: [
                        $class: 'ManuallyEnteredRepositorySource',
                        url: 'https://github.com/NPT0116/thanh-test-microservice.git'
                    ],
                    contextSource: [
                        $class: 'ManuallyEnteredCommitContextSource',
                        context: 'Jenkins/CI'
                    ],
                    statusResultSource: [
                        $class: 'ConditionalStatusResultSource',
                        results: [
                            [
                                $class: 'AnyBuildResult',
                                state: 'FAILURE',
                                message: 'Build failed or coverage below threshold!'
                            ]
                        ]
                    ]
                ])
            }
            echo 'Build failed!'
        }
    }
}
