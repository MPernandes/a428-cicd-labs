pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Checkout the main repository and initialize submodules
                git url: 'https://github.com/MPernandes/a428-cicd-labs.git', branch: 'main', poll: false
                sh 'git submodule update --init --recursive'
            }
        }
        stage('Build') {
            steps {
                // Build inside a Docker container
                script {
                    docker.image('node:16-buster-slim').inside() {
                        sh 'npm install'
                    }
                }
            }
        }
        stage('Test') { 
            steps {
                // Test step (run your tests inside the Docker container)
                script {
                    docker.image('node:16-buster-slim').inside() {
                        sh './jenkins/scripts/test.sh' 
                    }
                }
            }
        }
    }
}

