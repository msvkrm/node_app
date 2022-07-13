pipeline{
    agent {
        label 'worker'
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '2'))
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
    }
    stages{
        stage('Git Checkout') {
        steps {
                checkout scm
            }
        }

        stage('Build'){
            steps{
                sh 'sudo docker build -t node_app .'
                sh 'sudo docker tag node_app:latest 022536480424.dkr.ecr.us-east-1.amazonaws.com/node_app:latest'
                sh 'aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin 022536480424.dkr.ecr.us-east-1.amazonaws.com'
                sh 'sudo docker push 022536480424.dkr.ecr.us-east-1.amazonaws.com/node_app:latest'
            }
        }

        stage('Deploy'){
            // def dockerRun = 'docker pull 022536480424.dkr.ecr.us-east-1.amazonaws.com/node_app:latest'
            // sh'ssh -i ~/session.pem -o StrictHostKeyChecking=no ubuntu@ec2-44-208-23-240.compute-1.amazonaws.com ${dockerRun}'
            steps{
                script {
                    sh 'ssh -i ~/project2-key.pem -o StrictHostKeyChecking=no ubuntu@10.0.1.56'
                    // sh'''ssh -tt -i ~/session.pem -o StrictHostKeyChecking=no ubuntu@ec2-18-212-64-133.compute-1.amazonaws.com && aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 022536480424.dkr.ecr.us-east-1.amazonaws.com && docker pull 022536480424.dkr.ecr.us-east-1.amazonaws.com/node_app:latest && docker ps'''
                    sh 'ssh ubuntu@10.0.1.56 aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin 022536480424.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'ssh ubuntu@10.0.1.56 sudo docker pull 022536480424.dkr.ecr.us-east-1.amazonaws.com/node_app:latest'
                    sh 'ssh ubuntu@10.0.1.56 sudo docker images'
                    sh 'ssh ubuntu@10.0.1.56 sudo docker ps -q'
                    // sh 'ssh ubuntu@10.0.1.56 sudo docker kill $(ssh ubuntu@10.0.1.56 sudo docker ps -q)'
                    sh 'ssh ubuntu@10.0.1.56 sudo docker run -d -p8080:8080 022536480424.dkr.ecr.us-east-1.amazonaws.com/node_app:latest'
                }
                
            }
        }
    }
    post {
        always {
            deleteDir()
            sh 'sudo docker rmi 022536480424.dkr.ecr.us-east-1.amazonaws.com/node_app:latest'
        }

    }
}