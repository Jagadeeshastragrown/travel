pipeline {
    agent any

    environment {
        JAVA_HOME = "C:\\Program Files\\Java\\jdk-17"
        PATH = "${JAVA_HOME}\\bin;${env.PATH}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out the code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application...'
                bat 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                bat 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Find the first JAR file in the target directory
                    def jarFile = bat(script: 'for /r target %%i in (*.jar) do @echo %%i & exit /b', returnStdout: true).trim()

                    // Debug output to verify the correct JAR path
                    echo "Detected JAR: ${jarFile}"

                    // Ensure the JAR path is valid and exists
                    if (jarFile && fileExists(jarFile)) {
                        echo "Starting Spring Boot application..."
                        // Run the Spring Boot application synchronously
                        bat "java -jar \"${jarFile}\""
                    } else {
                        error("JAR file not found in target directory!")
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
