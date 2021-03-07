pipeline {
    
    agent any
    
    environment{
        dockerImage=''
        registry ='umahato/dockerfile1'
        registryCredential = 'dockerhub_id'
    }
    
    stages {
        stage('Checkout'){
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/umahato/devops-course-1']]])
            }
        }
        
        stage('Build Docker Image'){
            steps{
                script{
                    dockerImage=docker.build registry
                }
            }
        }
        
        stage('Uploading Image'){
            steps{
                script{
                        docker.withRegistry('',registryCredential){
                            dockerImage.push("$BUILD_NUMBER")
                            dockerImage.push('latest')
                        }
                    
                }
            }    
        }
        
        // Stopping Docker containers for cleaner Docker run
        stage('docker stop container') {
            steps {
                    sh 'docker ps -f name=myapacheContainer -q | xargs --no-run-if-empty docker container stop'
                    sh 'docker container ls -a -fname=myapacheContainer -q | xargs -r docker container rm'
            }
        }
    
        // Running Docker container, make sure port 8096 is opened in 
        stage('Docker Run') {
            steps{
                script {
                            dockerImage.run("-p 86:80 --rm --name myapacheContainer")
                }
            }
        }
    }
}
