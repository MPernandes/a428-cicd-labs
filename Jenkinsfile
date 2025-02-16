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
    stage('Deploy') {
            sh './jenkins/scripts/deliver.sh'
        input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)'
        docker.image('node:lts-buster-slim').inside('--network=host') {
            sh './jenkins/scripts/kill.sh'
        }
    }   
}