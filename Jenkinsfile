 pipeline {
    agent any

    environment {
      //ARTIFCATORY_ACCESS_TOKEN = credentials('jfrog-access-token')
      JFROG_URL = 'oluwatobi1.jfrog.io'
      JFROG_CREDENTIALS = credentials('jfrog-admin-credentials')
      //USERNAME = 'admin'
      //PASS = 'Pass1word'
      IMAGE_NAME = 'spring-petclinic'
      IMAGE_TAG = '3.3.0-SNAPSHOT'
      REPO_NAME = 'daniel'
      run_test = false
      
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
              expression { return run_test == "true" }
            }
            steps {
                // Run the Spring Boot application to verify it works
                bat './mvnw spring-boot:run'
                
                //bat 'java -jar target/*.jar &'
            }
        }

        stage('Test Login') {
            steps {
                bat """
                 echo Password Length: %JFROG_CREDENTIALS_PSW:~0,8% (Truncated)
                """
                
               
            }
        }

        stage('Artifactory Login') {
            steps {
                // Login to Artifactory
                bat """
                echo eyJ2ZXIiOiIyIiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYiLCJraWQiOiJCclZHLTFzczU1TXhKckx4aXRBNlNhSXpoVmlVMmg1SS1fSy1zV01vdE5ZIn0.eyJzdWIiOiJqZmFjQDAxamE0dnpyYTY2ejJ2MW1xNTllcDcxd3Y0L3VzZXJzL2FkbWluIiwic2NwIjoiYXBwbGllZC1wZXJtaXNzaW9ucy9hZG1pbiIsImF1ZCI6IipAKiIsImlzcyI6ImpmZmVAMDFqYTR2enJhNjZ6MnYxbXE1OWVwNzF3djQiLCJpYXQiOjE3MjkwMzI5MTIsImp0aSI6IjMyMjFiNWM3LWI4NTYtNDFmZi04NGRkLWEwZmFiYmEyMDMzYSIsInRpZCI6ImEwdXpvcmFubmRjdnAifQ.MruaY320BpTuMPq6rJ2l4hjMqeUArhcTeOYCPSwKYiOlkH4FF8v8iDD0F__JSCy-69-1V-rB5k3YUMvRiznd70jjiMB5s-PhzZ1ppwW8dks_O_V76GX-hq6XHaq0QQ6ewsAwBr8ndzJKwAScjHMCOfBRgO5PlJYgHkQPXrb5S2sVg6S0LCNjGNezIiD9zLNnQD29AYEuvzpMwmGgtLbGtg6aWAEPus6sJlw34ERv6H9woOmfwtxtRQRWwRshd-8huKhSBV4PkIraZszcgZyUxq-EA1r0e2nAkYu7lJ3cwZIx_dhkL8pKKN_Y5A47ZsqjNVYmGeagYS3U3Lchqxx8UA | docker login https://oluwatobi1.jfrog.io/artifactory -u admin --password-stdin
                """
            }
        }

        stage('Tag Image') {
            steps {
                // Tag the Docker image
                bat """
                docker tag %IMAGE_NAME%:%IMAGE_TAG% %JFROG_URL%/%REPO_NAME%/%IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

        stage('push image') {
            steps {
                // Push to Artifactory
                bat """
                docker push ${JFROG_URL}/${REPO_NAME}/${IMAGE_NAME}:${IMAGE_TAG}
                """                

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
