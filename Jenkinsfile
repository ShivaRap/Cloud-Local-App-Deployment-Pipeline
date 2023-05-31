pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-cred')
        AWS_SECRET_ACCESS_KEY = credentials('aws-cred')
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'gitcred', url: 'https://github.com/ShivaRap/Shiva-Rapol-IS27-devops-specialist-competition-req99120.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build("bc-express-app")
                }
            }
        }

        stage('Tag') {
            steps {
                script {
                    dockerImage.tag("860371349641.dkr.ecr.us-west-2.amazonaws.com/bc-express-app:jenkins-tag")
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('860371349641.dkr.ecr.us-west-2.amazonaws.com/bc-express-app', 'docker-cred') {
                        dockerImage.push("jenkins-tag")
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh("kubectl apply -f deployment.yml")
                }
            }
        }

	    stage('Wait for Deployment') {
            steps {
                sh 'sleep 20'
            }
        }
        
        stage('Check Health') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh("kubectl get pods -n default -l app=bc-express-app -o jsonpath='{.items[*].status.conditions[?(@.type==\"Ready\")].status}' | grep True")
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

