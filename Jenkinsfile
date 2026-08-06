pipeline {

    agent any

    tools {

        maven 'maven'

    }

    environment {
        TENANT_ID="6cd074d3-8bf8-4855-bd5a-ebea6660201c"
        IMAGE_NAME = "sprinbootapp"
        IMAGE_TAG = "latest"
        
    }

    stages {
        stage('Check Out from Git') 
        {
            steps {
                git branch: 'prod' , url: 'https://github.com/gitecob/proj3bc-springbootjavapp.git'
            }
        }

        stage('Maven Validate') 
        {
            steps {
                sh 'mvn validate'
            }
        }

            stage('Maven Compile') 
            {
                steps {
                    sh 'mvn compile'
                }
            }
        stage('Maven Test') 
        {
            steps {
                sh 'mvn test'
            }
        }
        stage('Maven Install') 
        {
            steps {
                sh 'mvn install'
            }
        }
        stage(' Trivy Scan')
        {
            steps {
                echo "Trivy Scan Started"
                sh 'trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL .'
                echo "Trivy Scan Finished"
            }
        }

        stage('Sonar Analysis')
        {
            environment {
                SCANNER_HOME = tool 'Sonar-scanner'
            }
          steps {
              withSonarQubeEnv('sonarserver') {
                sh '''${SCANNER_HOME}/bin/sonar-scanner \
                -Dsonar.organization=bkrrajmali \
                -Dsonar.projectName=springbootapp \
                -Dsonar.projectKey=springbootapp \
                -Dsonar.java.binaries=.
                '''
              }
            }
        }
        stage('Maven Package') 
        {
            steps {
                sh 'mvn package'
            }
        }
        stage('Sonar Quality Gate') 
        {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQuality abortPipeline: true, credentialsId: 'sonar'
                    echo "Sonar Quality Gate Finished"
            }
        }
      }
      stage ('Docker Build')
      {
        steps {
            
            echo "Build Docker Image"
            sh  'docker build -t "${IMAGE_NAME}:${IMAGE_TAG}" .'
      
        }
      }
   }
}
