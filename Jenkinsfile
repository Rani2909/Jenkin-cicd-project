pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Rani2909/Jenkin-cicd-project.git'
            }
        }

        stage('Test') {
            steps {
                sh 'pwd'
                sh 'ls -la'
            }
        }
    }
}