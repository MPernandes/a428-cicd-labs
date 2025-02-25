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
        docker.image('node:lts-buster-slim').inside('--network=host --user root -v /home/mpernandes/.ssh:/root/.ssh:ro') {
            sh './jenkins/scripts/deliver.sh'

            echo 'Aplikasi berjalan selama 1 menit...'
            sleep(time: 1, unit: 'MINUTES')

            sh './jenkins/scripts/kill.sh'

            echo 'Cek keberadaan SSH key di Docker container:'
            sh 'ls -lah /root/.ssh/'

            echo 'Menginstall openssh-client untuk scp...'
            sh 'apt-get update && apt-get install -y openssh-client'
            
            echo 'Mengupload hasil build ke EC2...'
 	    // sh 'scp -i /home/mpernandes/.ssh/dicodingmp.pem -r build/* ubuntu@35.93.43.239:/var/www/html/'
        sh 'scp -o StrictHostKeyChecking=no -i /root/.ssh/dicodingmp.pem -r build/* ubuntu@35.93.43.239:/var/www/html/'

        }

        echo 'Deploy ke EC2 selesai.'
    }
}