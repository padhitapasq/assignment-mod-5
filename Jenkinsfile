pipeline{
    //agent any
    agent {label 'worker'}
    options{
        buildDiscarder(logRotator(daysToKeepStr: '15'))
        disableConcurrentBuilds()
        timeout(time: 5, unit: 'MINUTES')
        retry (3)
    }
    parameters{
        string(name: 'BRANCH', defaultValue: 'master')
        }
   
    stages{
            stage('Git Checkout'){
        steps{
            checkout scm
        }
        }
     
       stage('Build and Push'){
        steps{
            sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 266948012606.dkr.ecr.us-east-1.amazonaws.com"
            sh "docker build -t 266948012606.dkr.ecr.us-east-1.amazonaws.com/assignment-mod5-tapas:v${BUILD_NUMBER} ."
            sh "docker push 266948012606.dkr.ecr.us-east-1.amazonaws.com/assignment-mod5-tapas:v${BUILD_NUMBER}"
        }
       }
       stage('Deploy'){
         steps{
             sh '''
             docker pull 266948012606.dkr.ecr.us-east-1.amazonaws.com/assignment-mod5-tapas:v${BUILD_NUMBER}
             if ("$( docker container inspect -f '{{.State.Running}}' mod5assignment )" == "true")
             then docker rm -f mod5assignment
             fi
             docker run -itd -p :3000 --name mod5assignment 266948012606.dkr.ecr.us-east-1.amazonaws.com/assignment-mod5-tapas:v${BUILD_NUMBER}
             '''
            }
        }
       
    }
    post{
        always{
            sh "echo Suceessful Running!!"
        }
    }

}
