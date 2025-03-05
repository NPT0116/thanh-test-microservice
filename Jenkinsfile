pipeline {
    agent any

    environment {
        // Thêm đường dẫn mong muốn vào biến PATH hiện tại của hệ thống
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
                // Chạy test bằng Maven
                bat 'mvn test'
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
                bat 'mvn clean package'
            }
        }
    }

    post {
        success {
            // Cập nhật trạng thái SUCCESS lên GitHub
            script {
                step([
                    $class: 'GitHubCommitStatusSetter',
                    reposSource: [
                        $class: 'ManuallyEnteredRepositorySource',
                        url: 'https://github.com/NPT0116/thanh-test-microservice.git' // Sửa lại URL repo nếu cần
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
            // Cập nhật trạng thái FAILURE lên GitHub
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
