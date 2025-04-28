pipeline {
    agent any
    
    tools {
        nodejs 'nodejs-10'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/pushpendrasingh3/jenkins-nodejs.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Docker Build and Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: '20b01989-52ca-4807-9331-ec9295d421fe', toolName: 'docker') {
                        sh "docker build -t demonodejs ."
                        sh "docker tag demonodejs pushpendrasingh3tcscom/nodejs:latest"
                        sh "docker push pushpendrasingh3tcscom/nodejs:latest"
                    }
                }
            }
        }
        
        stage('Docker Deploy') {
            steps {
                script{
                    withDockerRegistry(credentialsId: '20b01989-52ca-4807-9331-ec9295d421fe', toolName: 'docker') {
                        sh "docker run -d --name demo-nodejs -p 8081:8081 pushpendrasingh3tcscom/nodejs:latest"
                    }
                }
            }
        }
    }
}
