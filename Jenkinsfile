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

    }
}
