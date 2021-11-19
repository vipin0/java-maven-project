#!/usr/bin/env groovy

pipeline{
    agent any
    parameters {
            string(name: "BUILD_NUMBER", defaultValue: "1", trim: true, description: "Enter Build Number to deploy")
        }
    stages{
         stage('DEV'){
            steps{
                echo "Deploying to DEV environment"
                sh "sudo docker rm -f springboot-app-dev || true"
                sh "sudo docker run --rm -dp 4444:8080 --name springboot-app-dev vipin0/java-mvn:build-${params.BUILD_NUMBER}"
            }
        }
      stage('QA'){
            steps{
                echo "Deploying to QA environment"
                script {
                    env.APPROVE_QA = input message: 'Deploy to QA', ok: 'Continue',
                                parameters: [choice(name: 'APPROVE_QA', choices: 'YES\nNO', description: 'Deploy from DEV to QA?')]
                if (env.APPROVE_QA == 'YES'){
                    sh "sudo docker rm -f springboot-app-qa || true"
                    sh "sudo docker run --rm -dp 4444:8080 --name springboot-app-qa vipin0/java-mvn:build-${params.BUILD_NUMBER}"
                }else{
                    echo "Deploy aborted!!"
                }
            }
        }
      }
      stage('CLEAN UP'){
            steps{
                sh 'sudo docker image prune'
            }
        }
    }
}