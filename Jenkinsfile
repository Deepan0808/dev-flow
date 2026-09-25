pipeline {
    agent any
    
    tools{
      nodejs 'frontend'
    }
    
    environment {
      AWS_DEFAULT_REGION = 'us-east-2'
      S3_BUCKET = 'deploy-dpan'
      CLOUDFRONT_DIST_ID= 'E3IVNN8OTX80H7'
      AWS_CREDENTIALS= credentials('aws-id')
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
                dir('frontend') {
                    sh 'npm ci'
                }
            }
        }
        
        stage('build') {
            steps {
                dir('frontend') {
                    sh 'npm run build'
                 }
             }
        }
 
         
         stage('Sonarqube Analysis') {
            steps {
                script {
                    def scannerHome = tool name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                
             withSonarQubeEnv('SonarQube') {   
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
    }
    
       stage('Quality Gate') {
            steps {
            
                timeout(time: 5, unit: 'MINUTES') {
                
                    waitForQualityGate abortPipeline: true
 
                }
            }
        }
        
      stage('Deploy S3 Bucket'){
           steps{
               echo 'updating S3 Bucket'
               sh ''' 
               aws s3 sync frontend/dist/ \
               s3://${S3_BUCKET}/ \
               --delete \
               --region us-east-2
               '''
               echo 'Frontend Uploaded Successfully'
       }      
     }
     
     stage('Cloudfront Deployment'){
          steps{
              echo 'Deploying...'
              sh ''' 
              aws cloudfront create-invalidation \
              --distribution-id ${CLOUDFRONT_DIST_ID} \
              --paths "/*"
              '''
           }
       }
     }
 }       
       
