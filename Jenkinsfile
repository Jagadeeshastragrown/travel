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

                echo '🧪 Running springboot...'
                bat '''
                    start /b mvn spring-boot:run > output.log 2>&1
                    timeout /t 20
               '''
'
           }
        }
        stage('Check Application Status') {
            steps {
                script {
                    def response = bat(returnStdout: true, script: 'curl http://localhost:8025/api/hello')
                    echo "API Response: ${response}"
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
