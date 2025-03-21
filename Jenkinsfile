pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning the repository...'
                git 'https://github.com/Jagadeeshastragrown/travel.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Run Application') {
            steps {
                echo 'Stopping any existing application...'

                // Stop any process on port 8085
                sh '''
                PID=$(lsof -i :8085 -t) || true
                if [ ! -z "$PID" ]; then
                  echo "Stopping existing application (PID: $PID)"
                  kill -9 $PID
                fi
                '''

                echo 'Starting the application...'

                // Run the new application
                sh 'nohup java -jar target/*.jar > app.log 2>&1 &'
            }
        }
    }

    post {
        success {
            echo '✅ Application started successfully on port 8085!'
        }
        failure {
            echo '❌ Build or deployment failed.'
        }
    }
}
