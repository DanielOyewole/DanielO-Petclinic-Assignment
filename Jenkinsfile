 pipeline {
    agent any

    environment {
      //ARTIFCATORY_ACCESS_TOKEN = credentials('jfrog-access-token')
      JFROG_URL = 'oluwatobi1.jfrog.io'
      JFROG_CREDENTIALS = credentials('jfrog-admin-credentials')
      JFROG_PAT = credentials('admin-jfrog-access-token')
      IMAGE_NAME = 'spring-petclinic'
      IMAGE_TAG = '3.3.0-SNAPSHOT'
      REPO_NAME = 'daniel'
      run_deploy_test = false
      
    }

    stages {
        stage('Clean and Package') {
            steps {
                // Compile and test using the Spring Boot Maven plugin
                //sh './mvnw clean package'
                //bat './mvnw package'
                echo 'hi'

            }
        }

        stage('Build Docker Image') {
            steps {
                // Build a Docker image using the Spring Boot build plugin
                //bat './mvnw spring-boot:build-image'
                echo 'hi'
            }
        }

        stage('Run Application') {
            when {
              expression { return run_deploy_test == "true" }
            }
            steps {
                // Run the Spring Boot application to verify it works
                bat './mvnw spring-boot:run'
            }
        }

        stage('Artifactory Login') {
            steps {
                // Login to Artifactory
                bat """
                echo %JFROG_PAT_PSW% | docker login https://${JFROG_URL}/artifactory -u %JFROG_CREDENTIALS_USR% --password-stdin
                """
            }
        }

        stage('Tag Image') {
            steps {
                // Tag the Docker image
                bat """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${JFROG_URL}/${REPO_NAME}/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('push image') {
            steps {
                // Push to Artifactory
                bat """
                docker push ${JFROG_URL}/${REPO_NAME}/${IMAGE_NAME}:${IMAGE_TAG}
                """                
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
