pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
         stage('Clone repository') { 
            steps {
                script{
                checkout scm
                }
            }
        }

        stage('Build') {
            steps { 
                script{
                 app = docker.build("bc-express-app")
                }
            }
        }
        stage('Tag') {
            steps {
                script{
                    app.tag('jenkinstest')
                }
            }
        }        
        stage('Push') {
            steps {
                script{
                        docker.withRegistry('https://860371349641.dkr.ecr.us-west-2.amazonaws.com/bc-express-app', 'ecr:us-west-2:awsCred') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest2")
                    }
                }
            }
        }
      
        stage('Deploy'){
            steps {
                 sh 'kubectl apply -f deployment.yml'
            }
        }
        
        stage("Using curl example") {
            steps {
                script {
                    final String url = "adc1830dd0d7747e2917788baf46f4e1-1639727320.us-west-2.elb.amazonaws.com:3000/api/health"

                    final String response = sh(script: "curl -s $url", returnStdout: true).trim()

                    echo response
                }
            }
        }
           
    }
}
