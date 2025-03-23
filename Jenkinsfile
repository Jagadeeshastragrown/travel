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

                        // Start Spring Boot app in the background (non-blocking)
                        bat "start /b java -jar ${jarFile}"

                        echo "✅ Application deployed successfully!"
                    } else {
                        error("❌ JAR file not found: ${jarFile}")
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
