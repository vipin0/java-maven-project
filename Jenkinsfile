pipeline{
    agent any
    parameters {
        string(name: “BUILD_NUMBER”, defaultValue: “1", trim: true, description: “Enter Build Number”)
        }
    environment {
        AWS_ACCOUNT_ID=“853973692277"
        AWS_DEFAULT_REGION=“us-east-1”
        IMAGE_REPO_NAME=“myrepo”
        //IMAGE_TAG=“build-${params.BUILD_NUMBER}”
        REPOSITORY_URI = “${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}”
    }
    stages{
         stage(‘DEV’){
            steps{
                sh “sudo docker rm -f springboot-app || true”
                sh “sudo docker run --rm -dp 5555:8080 --name springboot-app ${REPOSITORY_URI}:build-${params.BUILD_NUMBER}”
            }
        }
      stage(‘QA’){
            steps{
                script {
                    env.APPROVE_QA = input message: ‘Deploy to QA’, ok: ‘Continue’,
                                parameters: [choice(name: ‘APPROVE_QA’, choices: ‘YES\nNO’, description: ‘Deploy from DEV to QA?’)]
                if (env.APPROVE_QA == ‘YES’){
                    sh “sudo docker rm -f springboot-app || true”
                    sh “sudo docker run --rm -dp 6666:8080 --name springboot-app ${REPOSITORY_URI}:build-${params.BUILD_NUMBER}”
                }
            }
        }
      }
      stage(‘CLEAN UP’){
            steps{
                sh ‘sudo docker image prune’
            }
        }
    }
}