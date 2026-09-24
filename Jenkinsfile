pipeline {
    agent any
    
    tools{
      nodejs 'frontend'
    }
 
    stages {
    
       stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'git-creds',
                    url: 'https://github.com/Deepan0808/full-deployment.git'
          }
        }
        
        stage('Install') {
            steps {
                sh 'npm install'
              }
         }
         
        stage('Build') {
            steps {
                sh 'npm run build'
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
                            -Dsonar.sources=frontend\
                            -Dsonar.host.url=http://localhost:9000 \
                            -Dsonar.login=${SONAR_TOKEN}
                       """
                    }
               } 
         }
    }
         
         stage('Quality Gate') {
           steps {
 
                timeout(time: 5, unit: 'MINUTES') {
 
                    waitForQualityGate abortPipeline: true
 
                }
            }
        }
     }
}
        
       
