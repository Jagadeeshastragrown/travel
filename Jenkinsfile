pipeline {
    agent any

    environment {
        APP_PORT = '8025'
        HEALTH_ENDPOINT = "/api/hello"
    }

    stages {
        stage('Checkout') {
            steps {
                echo '🚀 Cloning repository...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Building application...'
                bat 'mvn clean package'
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Starting Spring Boot in the background...'
                bat '''
                    start /b mvn spring-boot:run 1>output.log 2>&1
                    ping -n 5 127.0.0.1 > nul
                    wmic process where "commandline like '%spring-boot%'" get ProcessId > pid.txt
                '''
            }
        }

        stage('Check Application Status') {
            steps {
                script {
                    echo '🔍 Waiting for Spring Boot to start...'
                    sleep 40

                    def response = bat(script: "curl -s -o nul -w \"%{http_code}\" http://localhost:${APP_PORT}${HEALTH_ENDPOINT}", returnStdout: true).trim()

                    echo "👉 Raw curl output: ${response}"

                    if (response == '200') {
                        echo '✅ Application is up and running!'
                    } else {
                        error "❌ Application did not start properly. Response code: ${response}"
                    }
                }
            }
        }
    }

    post {
        always {
            echo '📃 Pipeline execution completed.'

            script {
                if (fileExists('output.log')) {
                    echo '📝 Spring Boot Logs:'
                    bat 'type output.log'
                }

                if (fileExists('pid.txt')) {
                    def pid = readFile('pid.txt').trim().split("\n")[1]
                    if (pid) {
                        echo "🛑 Stopping Spring Boot process: ${pid}"
                        bat "taskkill /PID ${pid} /F"
                    }
                }
            }
        }

        failure {
            echo '❌ Pipeline failed! Check logs above.'
        }

        success {
            echo '✅ Pipeline succeeded!'
        }
    }
}
