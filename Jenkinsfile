pipeline{
    agent any
    tools{
        maven 'Maven'
    }
    environment {
        GIT_TAG = "build-$BUILD_NUMBER"
    }
    stages{
        stage('build war'){
            steps{
                echo "Building war file"
                sh 'mvn clean package'
            }
        }
        stage('create git tag'){
            steps{
                sshagent (credentials: ['github-ssh-key']) {
                    sh "git tag -a $GIT_TAG -m 'Version $BUILD_NUMBER'"
                    sh('git push git@github.com:vipin0/java-maven-project.git HEAD:$BRANCH_NAME --tag  --force')
                
                }
            }
        }
        stage('build and push docker image'){
            steps{
                withCredentials([
                    usernamePassword(credentialsId: 'dockerhub-repo', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')
                ]){
                    sh('docker build -t $USERNAME/java-mvn:$GIT_TAG .')   
                    sh('echo $PASSWORD | docker login -u $USERNAME --password-stdin')
                    sh('docker push $USERNAME/java-mvn:$GIT_TAG')
                }
            }
        }
    }
    post{
        success{
            archiveArtifacts artifacts: '**/target/*.war', followSymlinks: false
            build job: 'asg-10-deploy-pipeline', parameters: [string(name: 'BUILD_NUMBER', value: "$BUILD_NUMBER")]
        }
    }
}
