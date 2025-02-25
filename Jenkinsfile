node {
    environment {
        CI = 'true'
        NODE_OPTIONS = '--openssl-legacy-provider' // Memperbaiki error ERR_OSSL_EVP_UNSUPPORTED
    }

    stage('Build') {
        docker.image('node:18').inside('--network=host') { // Gunakan node:18 untuk kompatibilitas
            sh 'npm install'
        }
    }

    stage('Test') {
        docker.image('node:18').inside('--network=host') {
            sh './jenkins/scripts/test.sh'
        }
    }

    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk lanjut)'
    }

    stage('Deploy') {
        docker.image('node:18').inside('--network=host --user root -v /home/mpernandes/.ssh:/root/.ssh:ro') {
            sh './jenkins/scripts/deliver.sh'

            echo 'Aplikasi berjalan selama 1 menit...'
            sleep(time: 1, unit: 'MINUTES')

            sh './jenkins/scripts/kill.sh'

            echo 'Cek keberadaan SSH key di Docker container:'
            sh 'ls -lah /root/.ssh/'

            echo 'Menginstall openssh-client untuk scp...'
            sh 'apt-get update && apt-get install -y --no-install-recommends openssh-client'

            echo 'Mengupload hasil build ke EC2...'
            sh 'scp -o StrictHostKeyChecking=no -i /root/.ssh/dicodingmp.pem -r build/* ubuntu@35.93.43.239:/var/www/html/'
        }

        echo 'Deploy ke EC2 selesai.'
    }
}