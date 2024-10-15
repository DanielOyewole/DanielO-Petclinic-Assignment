pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Clone the Spring Pet Clinic project from your GitHub repository
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                // Compile and test using the Spring Boot Maven plugin
                //bat './mvnw clean package -DskipTests'
                bat './mvnw package'

            }
        }

        stage('Build Docker Image') {
            steps {
                // Build a Docker image using the Spring Boot build plugin
                bat './mvnw spring-boot:build-image'
            }
        }

        stage('Docker Images') {
            steps {
                // Build a Docker image using the Spring Boot build plugin
                bat 'docker images'
            }
        }

        stage('Run Application') {
            steps {
                // Run the Spring Boot application to verify it works
                bat './mvnw spring-boot:run'
                //bat 'java -jar target/*.jar &'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished running.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed during Build and Test.'
        }
    }
}
