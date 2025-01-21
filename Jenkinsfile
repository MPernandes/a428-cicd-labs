pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Checkout the main repository and initialize submodules
                git url: 'https://github.com/MPernandes/a428-cicd-labs.git', branch: 'react-app', poll: false
                sh 'git submodule update --init --recursive'
            }
        }
        stage('Create package.json') {
            steps {
                // Create package.json if it doesn't exist
                script {
                    def packageJsonPath = '/var/jenkins_home/workspace/react-app/package.json'
                    if (!fileExists(packageJsonPath)) {
                        sh """
                            cd /var/jenkins_home/workspace/react-app
                            npm init -y
                        """
                    } else {
                        echo "package.json already exists"
                    }
                }
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
