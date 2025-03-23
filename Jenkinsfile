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
                script {
                    def jarFile = "target\\travel-jenkins-0.0.1-SNAPSHOT.jar"

                    if (fileExists(jarFile)) {
                        echo "🚀 Starting Spring Boot application with: ${jarFile}"

                        // Start Spring Boot in the background
                        bat "start /b java -jar ${jarFile} > output.log 2>&1"

                        echo "🕒 Waiting for Spring Boot to start..."

                        // Wait and check the logs until Spring Boot is ready
                        def maxAttempts = 30
                        def attempt = 0
                        def isRunning = false

                        while (attempt < maxAttempts) {
                            if (fileExists('output.log')) {
                                def logContent = readFile('output.log')
                                if (logContent.contains("Tomcat started on port")) {
                                    echo "✅ Spring Boot is running successfully!"
                                    isRunning = true
                                    break
                                }
                            }
                            sleep(time: 5, unit: 'SECONDS')
                            attempt++
                        }

                        if (!isRunning) {
                            error("❌ Spring Boot failed to start. Check output.log for details.")
                        }

                    } else {
                        error("❌ JAR file not found: ${jarFile}")
                    }
                }
            }
        }
    }

    post {
        always {
            echo '📝 Pipeline execution completed.'
            script {
                // Display the last 100 lines of the log for debugging
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
