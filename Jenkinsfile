node {
    def dockerImage = 'cimg/node:16.20'

    try {
        stage('Build') {
            docker.image(dockerImage).inside('-p 3000:3000 --user root') {
                withEnv(['CI=true', 'NODE_OPTIONS=--max-old-space-size=4096']) {
                    sh '''
                        rm -rf node_modules package-lock.json
                        npm cache clean --force
                        npm install
                        npm install @babel/core@^7.22.0 @babel/preset-env@latest --save-dev
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
                sh 'npm run build'
		//sh './jenkins/scripts/deliver.sh'

                //echo 'Aplikasi berjalan selama 1 menit...'
                //sleep(time: 1, unit: 'MINUTES')

                //sh './jenkins/scripts/kill.sh'
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

