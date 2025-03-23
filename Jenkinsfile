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
                def jarFile = bat(script: 'for /r target %i in (*.jar) do @echo %i', returnStdout: true).trim()
                echo "Detected JAR: ${jarFile}"
                bat "start java -jar ${jarFile}"
            }
          }
       }
   }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}