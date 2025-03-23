pipeline {
    agent any

    environment {
        APP_NAME = 'travel-api'
        APP_PORT = '8025'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Cloning the repository...'
                git branch: 'main', url: 'https://github.com/Jagadeeshastragrown/travel.git'
            }
        }

        stage('Build with Maven') {
            steps {
                echo 'Building the Spring Boot application...'
                bat 'mvn clean package'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running unit tests...'
                bat 'mvn test'
            }
        }

        stage('Deploy to Server') {
            steps {
                echo 'Stopping existing application...'
                bat '''
                for /f "tokens=5" %%a in ('netstat -aon ^| findstr :%APP_PORT%') do taskkill /PID %%a /F || echo No process found on port %APP_PORT%
                '''

                echo 'Deploying new version...'
                bat "start /B java -jar target\\*.jar --server.port=%APP_PORT% --server.address=0.0.0.0 > app.log 2>&1"
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
            echo "Access your API at: http://<your-windows-ip>:%APP_PORT%/api/hello"
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
