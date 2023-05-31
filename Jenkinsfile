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
//         stage('Push') {
//             steps {
//                 script{
//                         docker.withRegistry('https://860371349641.dkr.ecr.us-west-2.amazonaws.com/bc-express-app', 'credentials(awsCred)') {
//                     app.push("${env.BUILD_NUMBER}")
//                     app.push("latest2")
//                     }
//                 }
//             }
//         }
        stage('Push') {
            steps {
                script{
                    withCredentials([[
                        $class: 'AmazonWebServicesCredentialsBinding', 
                        accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                        credentialsId: 'awsCred', 
                        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                    ]]) {
                        sh """
                            $(aws ecr get-login --no-include-email --region us-west-2)
                            docker tag bc-express-app:latest 860371349641.dkr.ecr.us-west-2.amazonaws.com/bc-express-app:jenkinstest
                            docker push 860371349641.dkr.ecr.us-west-2.amazonaws.com/bc-express-app:jenkinstest
                        """
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
