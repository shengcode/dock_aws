pipeline {
    agent any

    environment {
        ImageRegistry = 'shengchunjob'
        EC2_IP = '3.141.14.116'
        DockerComposeFile = 'docker-compose.yml'
        DotEnvFile = '.env'
    }

    stages {
      
        stage("SCM Checkout"){
        steps{
        powershell 'echo "Hello from the workspace"'
         git branch: 'main', credentialsId: 'PAT_Jenkins', url: 'https://github.com/shengcode/dock_aws.git'
        }
    }

        stage("buildImage") {
            steps {
                script {
                    powershell 'echo "Building Docker Image..."'
                    powershell "docker build -t ${ImageRegistry}/${JOB_NAME}:${BUILD_NUMBER} ."
                }
            }
        }

        stage("pushImage") {
            steps {
                script {
                    powershell 'echo "Pushing Image to DockerHub..."'
                   
                        powershell "docker login -u shengchunjob -p ShengZhaogongzuo@12"
                        powershell "docker push ${ImageRegistry}/${JOB_NAME}:${BUILD_NUMBER}"
                    
                }
            }
        }

        stage("deployCompose") {
            steps {
                script {
                    powershell 'echo "Deploying with Docker Compose..."'
                   
                        // Upload files once to reduce redundant SCP commands
                        powershell """
                        scp -o StrictHostKeyChecking=no ${DotEnvFile} ${DockerComposeFile} ubuntu@${EC2_IP}:/home/ubuntu
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} "docker compose -f /home/ubuntu/${DockerComposeFile} --env-file /home/ubuntu/${DotEnvFile} down"
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} "docker compose -f /home/ubuntu/${DockerComposeFile} --env-file /home/ubuntu/${DotEnvFile} up -d"
                        """
                    
                }
            }
        }
    }
}
