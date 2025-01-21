<<<<<<< HEAD
    pipeline {
        agent {
            docker {
                image 'node:16-buster-slim'
                args '-p 3000:3000'
=======
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // Checkout the main repository and initialize submodules
                git url: 'https://github.com/MPernandes/a428-cicd-labs.git', branch: 'react-app', poll: false
                sh 'git submodule update --init --recursive'
>>>>>>> 2bfbe086c0793e2feb29627d360002aee1838dc4
            }
        }
        stages {
            stage('Build') {
                steps {
                    sh 'npm install'
                }
            }
            stage('Test') { 
                steps {
                    sh './jenkins/scripts/test.sh' 
                }
            }
        }
    }