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
        bat 'mvn test'
    }asdfasdf sdf
    post {
        always {
            junit '**/target/surefire-reports/*.xml'
            jacoco(
                execPattern: '**/target/jacoco.exec',
                classPattern: '**/target/classes',
                sourcePattern: '**/src/main/java'
            )

            // Lưu trữ file báo cáo test làm artifact
            archiveArtifacts artifacts: '**/target/surefire-reports/*.xml', allowEmptyArchive: true
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
