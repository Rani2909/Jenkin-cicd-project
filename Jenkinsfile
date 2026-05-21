pipeline {
    agent any

    
    environment {
        IMAGE_NAME = "rani2909/jenkins-cicd-project"
        TAG = "latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Rani2909/Jenkin-cicd-project.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    script {
                        def scannerHome = tool 'sonar-scanner'

                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                sh 'trivy fs .'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$TAG'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment-service.yml'
            }
        }

        stage('Restart Deployment') {
            steps {
                sh 'kubectl rollout restart deployment flask-app'
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline Executed Successfully!'
        }

        failure {
            echo 'Pipeline Failed!'
        }
    }
}