pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = "kavya699"
        APP_NAME = "doctorify"
        IMAGE_TAG = "latest"
        KUBECONFIG = "/home/ubuntu/.kube/config"   // path to kubeconfig on EC2
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/kavyasri017/agile-health-care.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_HUB_USER/$APP_NAME:$IMAGE_TAG ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push $DOCKER_HUB_USER/$APP_NAME:$IMAGE_TAG"
                }
            }
        }

            stage('Deploy to Kubernetes') {
    steps {
        sh '''
          export KUBECONFIG=/home/ubuntu/.kube/config
          kubectl apply -f k8s-deployment.yml
        '''
               }
            }
        }
    }
}
