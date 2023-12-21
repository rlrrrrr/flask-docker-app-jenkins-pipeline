pipeline {
    agent any
    environment {
        DOCKER_HUB_REPO = "rlrrr/jenkins"
        CONTAINER_NAME = "flask-container"
        STUB_VALUE = "200"

    }
    stages {
        stage('Stubs-Replacement'){
            steps {
                checkout scm

                docker.withRegistry('https://registry.example.com', 'docker-hub-credentials')
                // 'STUB_VALUE' Environment Variable declared in Jenkins Configuration 
                echo "STUB_VALUE = ${STUB_VALUE}"
                sh "sed -i 's/<STUB_VALUE>/$STUB_VALUE/g' config.py"
                sh 'cat config.py'
            }
        }
        stage('Build') {
            steps {

                // login to docker HUB

                docker login -u $USERNAME -p $PASSWORD
                //  Building new image
                sh 'docker image build -t $DOCKER_HUB_REPO:latest .'
                sh 'docker image tag $DOCKER_HUB_REPO:latest $DOCKER_HUB_REPO:$BUILD_NUMBER'

                //  Pushing Image to Repository
                sh 'docker push $CONTAINER_NAME:$BUILD_NUMBER'
                sh 'docker push $CONTAINER_NAME:latest'
                
                echo "Image built and pushed to repository"
            }
        }
        stage('Deploy') {
            steps {
                script{
                    try {
                        sh 'docker stop $CONTAINER_NAME'
                        sh 'docker rm $CONTAINER_NAME'
                    } catch (Exception e) {
                        println("No existing container to stop and remove.")
                    }
                    sh 'docker run --name $CONTAINER_NAME -d -p 5000:5000 $DOCKER_HUB_REPO'
                }
            }
        }
    }
}
