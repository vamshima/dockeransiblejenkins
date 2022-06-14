pipeline{
    agent any
    
    tools {
      maven 'maven'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/vamshima/dockeransiblejenkins.git'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t vamshima/jen-doc:${DOCKER_TAG} "
            }
        }
        
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'Docker-password', variable: 'dockerhub')]) {
                    sh "docker login -u vamshima -p ${dockerhub}"
                }
                
                sh "docker push vamshima/jen-doc:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
                
            ansiblePlaybook credentialsId: 'Ansible-dev', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml' 
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
