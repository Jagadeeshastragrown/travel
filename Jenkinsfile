pipeline {
    agent any

    environment {
        JAVA_HOME = "C:\\Program Files\\Java\\jdk-17"
        PATH = "${JAVA_HOME}\\bin;${env.PATH}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Checking out the code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Building the application...'
                bat 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running unit tests...'
                bat 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Starting Spring Boot in the background...'

                bat '''
                    start /b mvn spring-boot:run > output.log 2>&1
                    ping -n 5 127.0.0.1 > nul
                    wmic process where "commandline like '%%spring-boot%%'" get ProcessId > pid.txt
                '''
            }
        }

        stage('Check Application Status') {
            steps {
                script {
                    echo '🔍 Waiting for Spring Boot to start...'

                    def maxRetries = 5
                    def retries = 0
                    def appUp = false

                    while (retries < maxRetries) {
                        sleep(time: 15, unit: 'SECONDS') // Wait for the app to start

                        def response = bat(returnStdout: true, script: 'curl -s -o /dev/null -w "%{http_code}" http://localhost:8025/api/hello').trim()

                        echo "API Response Code: ${response}"

                        if (response == '200') {
                            echo '✅ Spring Boot is UP!'
                            appUp = true
                            break
                        }

                        retries++
                        echo "Retrying... (${retries}/${maxRetries})"
                    }

                    if (!appUp) {
                        error('❌ Spring Boot did not start successfully!')
                    }
                }
            }
        }

        stage('Stop Spring Boot') {
            steps {
                echo '🛑 Stopping Spring Boot application...'

                script {
                    if (fileExists('pid.txt')) {
                        def pid = readFile('pid.txt').trim().split('\n')[1]
                        echo "Stopping Process ID: ${pid}"
                        bat "taskkill /PID ${pid} /F"
                    } else {
                        echo "❌ PID file not found. Spring Boot may not be running."
                    }
                }
            }
        }
    }

    post {
        always {
            echo '📝 Pipeline execution completed.'
            script {
                if (fileExists('output.log')) {
                    bat 'type output.log'
                }
            }
        }
        success {
            echo '✅ Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed! Check logs above.'
        }
    }
}
