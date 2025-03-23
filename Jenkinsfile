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

                // Start Spring Boot in the background and save the PID
                bat '''
                    start /b mvn spring-boot:run > output.log 2>&1
                    wmic process where "commandline like '%%spring-boot%%'" get ProcessId > pid.txt
                '''
            }
        }

        stage('Check Application Status') {
            steps {
                script {
                    echo '🔍 Checking if Spring Boot is running...'
                    sleep(time: 10, unit: 'SECONDS') // Wait for Spring Boot to start
                    def response = bat(returnStdout: true, script: 'curl http://localhost:8025/api/hello')
                    echo "API Response: ${response}"
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
            echo '❌ Pipeline failed!'
        }
    }
}
