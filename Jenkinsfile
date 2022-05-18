pipeline {
    agent any
    environment {
        TAG = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Code Checkout') {
            agent {
                label 'SonarQube on AWS'
            }
            steps {
                git 'https://github.com/shashank-ssriva/Hello-World-DevOps-Training.git'
            }
        }
        stage('SonarQube Analysis') {
            agent {
                label 'SonarQube on AWS'
            }
            steps {
                withSonarQubeEnv('SonarCloud') {
                echo env.BUILD_NUMBER
                echo "${TAG}"
                sh "/usr/bin/envsubst < sonar-project.properties > /tmp/sonar-project.properties"
                sh "mv /tmp/sonar-project.properties sonar-project.properties"
                sh "cat sonar-project.properties"
                sh "/opt/sonar-scanner/bin/sonar-scanner"
            }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t shashankssriva/hello-world-lite-arm64:1.0.${BUILD_NUMBER} .'
            }
        }

        stage('Push Docker Image to Registry') {
            steps {
                sh 'docker push shashankssriva/hello-world-lite-arm64:1.0.${BUILD_NUMBER}'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh "/opt/homebrew/bin/envsubst < helloworld-deployment.yml > /tmp/helloworld-deployment.yml"
                sh "mv /tmp/helloworld-deployment.yml helloworld-deployment.yml"
                sh 'kubectl apply -f helloworld-deployment.yml'
            }
        }
    }
}