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

    stage('Manual Approval') {
          
            input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk lanjut)'
        
    }

    stage('Deploy') {
        docker.image('node:lts-buster-slim').inside('--network=host') {
            sh './jenkins/scripts/deliver.sh'

            echo 'Aplikasi berjalan selama 1 menit...'
            sleep(time: 1, unit: 'MINUTES')

            sh './jenkins/scripts/kill.sh'
        }
    }
}
