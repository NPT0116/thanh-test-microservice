pipeline {
    agent any

    // Thêm Maven vào PATH cho toàn bộ pipeline
    environment {
        // Lưu ý: PATH đã có sẵn nên dùng cách nối thêm đường dẫn vào PATH hiện tại.
        PATH = "${env.PATH}:/opt/homebrew/Cellar/maven/3.9.9/libexec/bin"
    }

    stages {
        stage('Test') {
            steps {
                // Checkout mã nguồn
                checkout scm

                // In phiên bản Maven để kiểm tra PATH đã được cấu hình đúng chưa
                sh 'mvn --version'

                // Chạy build với mục đích test (không dừng nếu test thất bại)
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
            post {
                success {
                    // Thu thập báo cáo test
                    junit '**/target/surefire-reports/*.xml'
                    
                    // Thu thập báo cáo coverage của JaCoCo từ tất cả các module
                    recordCoverage(
                        tools: [[parser: 'JACOCO']],
                        id: 'jacoco', 
                        name: 'JaCoCo Coverage',
                        sourceCodeRetention: 'EVERY_BUILD',
                        qualityGates: [
                            [threshold: 70.0, metric: 'LINE', baseline: 'PROJECT', unstable: false],
                            [threshold: 70.0, metric: 'BRANCH', baseline: 'PROJECT', unstable: false]
                        ]
                    )

                    // Cập nhật trạng thái commit trên GitHub dựa theo kết quả build
                    step([
                        $class: 'GitHubCommitStatusSetter',
                        reposSource: [$class: 'ManuallyEnteredRepositorySource', url: 'https://github.com/pinkWar123/spring-petclinic-microservices.git'],
                        contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'Coverage'],
                        statusResultSource: [
                            $class: 'ConditionalStatusResultSource',
                            results: [
                                [
                                    $class: 'AnyBuildResult',
                                    state: 'SUCCESS',
                                    message: 'All builds passed!'
                                ],
                                [
                                    $class: 'UnstableBuildResult',
                                    state: 'FAILURE',
                                    message: 'Coverage below threshold!'
                                ],
                                [
                                    $class: 'FailedBuildResult',
                                    state: 'FAILURE',
                                    message: 'Build failed!'
                                ]
                            ]
                        ]
                    ])

                    // Công bố kết quả kiểm tra
                    publishChecks name: 'Coverage', summary: "Coverage is ${env.COVERAGE}%"
                }
            }
        }
        stage('Build') {
            steps {
                // Build lại dự án (package tất cả modules)
                sh 'mvn -B package'
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
