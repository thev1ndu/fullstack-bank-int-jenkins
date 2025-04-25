pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/thev1ndu/fullstack-bank-int-jenkins.git'
            }
        }
        
        stage('Pull Trivy Docker Image') {
            steps {
                sh 'docker pull aquasec/trivy'
            }
        }
        
        stage('Trivy File System Scan') {
            steps {
                sh '''
                    docker run --rm -v $(pwd):/project aquasec/trivy fs .
                '''
            }
        }

        stage('OWASP File System Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }

        stage('Backend') {
            steps {
                dir('app/backend') {
                    sh "npm install"
                }
            }
        }

        stage('Frontend') {
            steps {
                dir('app/frontend') {
                    sh "npm install"
                }
            }
        }

        stage('Deploy to Container') {
            steps {
                sh "npm run compose:up -d"
            }
        }
    }
}
