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


                echo 'Deploying new version...'
                bat "java -jar travel-jenkins-0.0.1-SNAPSHOT.jar --server.port=%APP_PORT% --server.address=0.0.0.0 > app.log 2>&1"
            }
        }
    }

      post {
            success {
                script {
                    def ipAddress = powershell(returnStdout: true, script: "(Get-NetIPAddress -AddressFamily IPv4).IPAddress | Select-String -Pattern '^192|^10'").trim()
                    echo 'Deployment successful!'
                    echo "Access your API at: http://${ipAddress}:${APP_PORT}/api/hello"
                }
            }
            failure {
                echo 'Deployment failed!'
            }
      }
   }