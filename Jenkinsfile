pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/shekar55/webapp1.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }
            stage('Quality Gate Status'){
                
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                    }
                }
            }
        stage('upload war file to nexus'){
            
            steps{
                script{
     
                    nexusArtifactUploader artifacts:
                        [
                            [
                                artifactId: 'springboot', 
                                classifier: '', 
                                file: 'target/Uber.jar',
                                type: 'jar'
                            ]
                        ], 
                        credentialsId: 'nexus',
                        groupId: 'com.example',
                        nexusUrl: '13.233.58.184:8081',
                        nexusVersion: 'nexus3',
                        protocol: 'http', 
                        repository: 'demoapp-release',
                        version:  '1.0.1'
                }  
            }   
        }
        
          stage('Docker image build'){
            
            steps{
                
                script{
                    
                    sh 'docker build -t webapp .'
                    sh 'docker image tag webapp 13.233.58.184:8085/webapp:v1'
   
                    
                }
             }    
         }
        
        stage("Docker Push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'git_cred', variable: 'dockernex')]) { #use secrettext
                   sh "docker login -u admin -p ${dockernex} 13.233.58.184:8085"
        }
        sh "docker push 13.233.58.184:8085/webapp:v1"
        } 
            }   
    }
 }            
}
 
