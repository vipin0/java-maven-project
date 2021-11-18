pipeline{
    agent any
    tools{
        maven 'Maven'
    }
    stages{
        stage('build war'){
            steps{
                echo "Building war file"
                sh 'mvn clean package'
            }
        }
        stage('build docker image'){
            steps{
                echo "Buildind docker image"
                sh 'docker build -t java-mvn:0.1 .'
            }
        }
        stage('deploy'){
            steps{
                echo "Deploying application"
                sh 'docker rm -f java-mvn-app'
                sh 'docker run --rm -dp 4444:8080 --name java-mvn-app java-mvn:0.1'
                echo "Application is live on <ip-address>:4444"
            }
        }
    }
    post{
        success{
            archiveArtifacts artifacts: '**/target/*.war', followSymlinks: false
        }
    }
}
