pipeline {
    agent any
    
    tools{
      nodejs 'frontend'
    }
    
    environment {
      AWS_DEFAULT_REGION = 'us-east-1'
      
      S3_BUCKET = 'frontend'
      }
 
    stages {
    
       stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'git-creds',
                    url: 'https://github.com/Deepan0808/dev-flow.git'
          }
        }
        
        stage('Install') {
            steps {
                sh 'npm install'
              }
         }
         
         stage('Sonarqube Analysis') {
            steps {
                script {
                    def scannerhome = tool name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh """
                            ${scannerhome}/bin/sonar-scanner \
                            -Dsonar.projectKey=frontend \
                            -Dsonar.sources=frontend \
                            -Dsonar.host.url=http://localhost:9000 \
                            -Dsonar.login=${SONAR_TOKEN}
                            """
                    }
               } 
         }
    }
}
