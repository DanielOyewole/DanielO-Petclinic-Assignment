 pipeline {
    agent any

    environment {
      //ARTIFCATORY_ACCESS_TOKEN = credentials('jfrog-access-token')
      JFROG_URL = 'guddytech.jfrog.io'
      JFROG_CREDENTIALS = credentials('jfrog-admin-credentials')
      //USERNAME = 'admin'
      //PASS = 'Pass1word'
      IMAGE_NAME = 'bole'
      IMAGE_TAG = '2.0'
      REPO_NAME = 'dock'
      run_test = false
    }

    stages {
        stage('Clean and Package') {
            steps {
                // Compile and test using the Spring Boot Maven plugin
                sh 'chmod +x ./mvnw'
                //sh './mvnw clean package'
                //bat './mvnw package'

            }
        }

        stage('Build Docker Image') {
            steps {
                // Build a Docker image using the Spring Boot build plugin
                echo 'hello'
                //sh './mvnw spring-boot:build-image'
            }
        }

        stage('Run Application') {
            when {
              expression { return run_test == "true" }
            }
            steps {
                // Run the Spring Boot application to verify it works
                echo 'run'
                sh './mvnw spring-boot:run'
                
                //bat 'java -jar target/*.jar &'
            }
        }

        stage('Artifactory Login') {
            steps {
                // Login to Artifactory
                sh """
                echo '${JFROG_CREDENTIALS_PSW}' | docker login 'https://${JFROG_URL}/artifactory' -u '${JFROG_CREDENTIALS_USR}' --password-stdin
                """
            }
        }

        stage('Tag Image') {
            steps {
                // Tag the Docker image
                sh """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${JFROG_URL}/${REPO_NAME}/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('push image') {
            steps {
                // Push to Artifactory
                sh """
                docker push ${JFROG_URL}/${REPO_NAME}/${IMAGE_NAME}:${IMAGE_TAG}
                """
                //docker push guddytech.jfrog.io/dock/bole:1.0
                

                //sh 'jfrog rt upload --url https://guddytech.jfrog.io/artifactory/ --access-token ${ARTIFCATORY_ACCESS_TOKEN} target/*.jar java-web-app/'
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
