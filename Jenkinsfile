node {
    environment {
        CI = 'true'
    }
    
    stage('Build') {
        docker.image('node:lts-buster-slim').inside('--network=host') {
            sh 'npm install'
        }
    }
    
    stage('Test') {
        docker.image('node:lts-buster-slim').inside('--network=host') {
            sh './jenkins/scripts/test.sh'
        }
    }
}