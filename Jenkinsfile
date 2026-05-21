pipeline {
agent any

```
tools {
    git 'Default'
}

environment {
    SCANNER_HOME = tool 'sonar-scanner'
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
                sh '''
                $SCANNER_HOME/bin/sonar-scanner \
                -Dsonar.projectKey=jenkins-cicd-project \
                -Dsonar.projectName=jenkins-cicd-project \
                -Dsonar.sources=. \
                -Dsonar.host.url=http://host.docker.internal:9000
                '''
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
            sh 'docker build -t rani2909/jenkins-cicd-project:latest .'
        }
    }

    stage('Docker Login') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: 'dockerhub',
                passwordVariable: 'DOCKER_PASSWORD',
                usernameVariable: 'DOCKER_USERNAME'
            )]) {

                sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
            }
        }
    }

    stage('Push Image') {
        steps {
            sh 'docker push rani2909/jenkins-cicd-project:latest'
        }
    }

    stage('Deploy to Kubernetes') {
        steps {
            sh 'kubectl apply -f deployment.yaml'
            sh 'kubectl apply -f service.yaml'
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
        echo 'Pipeline Successfully Executed!'
    }

    failure {
        echo 'Pipeline Failed!'
    }
}
```

}
