pipeline {
    agent any 

    environment {
        IMAGE = "pranitpotsure/hello-java:${BUILD_NUMBER}"
        TARGET = "ec2-user@3.108.41.40"
    } 
    
    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                git 'https://github.com/pranitpotsure/containerized-java-pipeline.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'cd hello-world && mvn clean package'
            }
        }

        stage('Build Docker Image') { 
            steps { 
                sh 'cd hello-world && docker build -t $IMAGE .'
            }
        }

        stage('Push Docker') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-pass',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['target-ec2-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $TARGET "
                    docker pull $IMAGE &&
                    docker stop hello-java || true &&
                    docker rm hello-java || true &&
                    docker run -d -p 8080:8080 --name hello-java $IMAGE
                    "
                    '''
                }
            }
        }

    }
}