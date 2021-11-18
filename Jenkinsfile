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
                    // sh 'git add .'
                    // sh('git commit -m "bumped to $GIT_TAG"')
                    sh "git tag -a $GIT_TAG -m 'Version $BUILD_NUMBER'"
                    sh('git push git@github.com:vipin0/java-maven-project.git HEAD:$BRANCH_NAME --tag')
                
                }
            }
        }
        stage('build and push docker image'){
            steps{
                withCredentials([
                    usernamePassword(credentialsId: 'dockerhub-repo', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')
                ]){
                    sh('docker build -t $USERNAME/java-mvn-app:$GIT_TAG .')
                    // don't use this
//                     sh "echo $PASSWORD | docker login -u $USERNAME --password-stdin"   
                    sh('echo $PASSWORD | docker login -u $USERNAME --password-stdin')
                    sh('docker push $USERNAME/java-mvn-app:$GIT_TAG')
                }
            }
        }
        stage('deploy'){
            steps{
                echo "Deploying application"
                sh 'docker rm -f java-mvn-app1'
                sh ('docker run --rm -dp 3000:8080 --name java-mvn-app1 vipin0/java-mvn-app:$GIT_TAG')
                echo "Application is live on <ip-address>:3000"
            }
        }
    }
    post{
        success{
            archiveArtifacts artifacts: '**/target/*.war', followSymlinks: false
        }
    }
}
