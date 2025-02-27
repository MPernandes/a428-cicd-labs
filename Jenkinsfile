node {
    def dockerImage = 'node:lts'

    try {
        stage('Build') {
            docker.image(dockerImage).inside('-p 3000:3000 --user root') {
                withEnv(['CI=true', 'NODE_OPTIONS=--max-old-space-size=2048']) {
                    sh '''
                        # Hanya hapus node_modules jika perlu (lebih cepat)
                        if [ -d "node_modules" ]; then
                          echo "✅ node_modules ditemukan, tidak perlu install ulang"
                        else
                          echo "🚀 node_modules tidak ditemukan, install ulang"
                          rm -rf package-lock.json
                          npm cache clean --force
                          npm ci
                        fi
                    '''
                }
            }
        }

        stage('Test') {
            docker.image(dockerImage).inside('--user root') {
                sh './jenkins/scripts/test.sh'
            }
        }

        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk lanjut)'
        }

        stage('Deploy') {
            docker.image(dockerImage).inside('-p 3000:3000 --user root') {
                withEnv(['NODE_OPTIONS=--max-old-space-size=2048']) {
                    sh '''
                        echo "🚀 Memulai proses build"
                        npm run build
                    '''
                }

                // Opsional: Jalankan deploy script jika ada
                // sh './jenkins/scripts/deliver.sh'
            }
        }

    } catch (Exception e) {
        echo "❌ Pipeline gagal: ${e}"
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        echo '✅ Pipeline selesai.'
    }
}

