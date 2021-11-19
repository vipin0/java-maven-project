#!/usr/bin/env groovy

pipeline{
    agent any
    parameters {
            string(name: "BUILD_NUMBER", defaultValue: "1", trim: true, description: "Enter Build Number to deploy")
        }
    stages{
         stage('DEV'){
            steps{
                echo "Deploying to DEV environment..."
                sh "docker rm -f springboot-app-dev || true"
                sh "docker run --rm -dp 4444:8080 --name springboot-app-dev vipin0/java-mvn:build-${params.BUILD_NUMBER}"
                echo "Application live on <your-ip>:4444"
            }
        }
      stage('QA'){
            steps{
                echo "Deploying to QA environment..."
                script {
                    env.APPROVE_QA = input message: 'Deploy to QA', ok: 'Continue',
                                parameters: [choice(name: 'APPROVE_QA', choices: 'YES\nNO', description: 'Deploy from DEV to QA?')]
                if (env.APPROVE_QA == 'YES'){
                    sh "docker rm -f springboot-app-dev || true"  // for removing dev container
                    sh "docker rm -f springboot-app-qa || true"
                    sh "docker run --rm -dp 5000:8080 --name springboot-app-qa vipin0/java-mvn:build-${params.BUILD_NUMBER}"
                    echo "Application live on <your-ip>:5000"
                }else{
                    echo "Deploy aborted!!"
                }
            }
        }
      }
      stage('CLEAN UP'){
            steps{
                echo "Removing dangling images..."
                sh 'docker image prune -f'
            }
        }
    }
}