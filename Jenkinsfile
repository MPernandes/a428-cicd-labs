node {
    stage('Docker Environment') {
        steps {
            docker.image('node:lts-buster-slim').inside('-p 3000:3000') {
                echo 'Docker container is running'

                stage('Build') {
                    sh 'npm install'
                }

                stage('Test') {
                    sh './jenkins/scripts/test.sh'
                }

                stage('Manual Approval') {
                    input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
                }

                stage('Deploy') {
                    sh './jenkins/scripts/deliver.sh'

                    echo 'Menunggu 1 menit agar aplikasi berjalan...'
                    sleep(time: 1, unit: 'MINUTES')
                    echo 'Proses deploy selesai, pipeline berhasil!'

                    input message: 'Finished using the website? (Click "Proceed" to continue)'
                    sh './jenkins/scripts/kill.sh'
                }
            }
        }
    }
}