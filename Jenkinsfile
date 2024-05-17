pipeline{

    agent any

    environment {
        BRANCH_NAME = "${GIT_BRANCH.split("/")[1]}"
    }

    stages{
        stage('Echo') {
            steps{
                sh "echo Je suis là-bas Abi!"
                }
            }
        stage('Show Release') {
            steps {
                sh "cat /etc/os-release"
            }
        }
        stage('Initialisation') {
            steps {
                sh "make clean"
                sh "make venv" 
                sh "make install"
            }
        }

        stage('Unit Test') {
            steps {
                sh "make test"
            }
        }

        stage('Build docker image') {
            steps {
                sh "docker ps -a" 
                sh "echo $BRANCH_NAME"
                sh "echo $env.GIT_BRANCH"
                sh "printenv"
                sh "docker build -t ajanadi/${BRANCH_NAME}_default_image ."
            }
        }


        stage('Test docker image') { 
            steps {
                sh "docker container stop default_container_$env.BRANCH_NAME"
                sh "docker container rm default_container_$env.BRANCH_NAME"
                sh "docker run -d -p 5000:8000 --name default_container_$env.BRANCH_NAME ajanadi/${BRANCH_NAME}_default_image"
            }
        }

                
        stage('Cleanup ') {
            steps {
                sh "docker container stop default_container_$env.BRANCH_NAME"
                sh "docker container rm default_container_$env.BRANCH_NAME" 
            }
        }

        stage("Push image to Docker Hub"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'DOCKER_CREDS_AJANADI', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]){
                    sh """
                    docker login  --username $USERNAME --password $PASSWORD && docker push ajanadi/${BRANCH_NAME}_default_image:latest
                    """
                }
            }
        }
    } 
   
}
