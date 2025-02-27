node {
    def dockerImage = 'node:lts'

    try {
        stage('Build') {
            docker.image(dockerImage).inside('-p 3000:3000 --user root') {
                withEnv(['CI=true', 'NODE_OPTIONS=--max-old-space-size=2048']) {
                    sh '''
                        echo "🚀 Memulai tahap Build"

                        # Periksa apakah node_modules sudah ada
                        if [ -d "node_modules" ]; then
                            echo "✅ node_modules ditemukan, melewati instalasi."
                        else
                            echo "🚀 node_modules tidak ditemukan, memulai instalasi."
                            rm -rf package-lock.json
                            npm cache clean --force
                            npm install
                        fi

                        # Pastikan react-scripts terinstall
                        if [ ! -d "node_modules/react-scripts" ]; then
                            echo "⚠️ react-scripts tidak ditemukan, menginstall ulang."
                            npm install react-scripts --save-dev
                        fi
                    '''
                }
            }
        }

        stage('Test') {
            docker.image(dockerImage).inside('--user root') {
                withEnv(['CI=true']) {
                    sh '''
                        echo "🧪 Menjalankan pengujian."

                        if [ ! -f "./jenkins/scripts/test.sh" ]; then
                            echo "❌ File test.sh tidak ditemukan."
                            exit 1
                        fi
			
			if [ ! -f "node_modules/.bin/react-scripts" ]; then
        	    	echo "⚠️ react-scripts tidak ditemukan, mencoba install ulang"
              		npm install
            		fi
                        chmod +x ./jenkins/scripts/test.sh
                        ./jenkins/scripts/test.sh
                    '''
                }
            }
        }

        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk lanjut)'
        }

        stage('Deploy') {
            docker.image(dockerImage).inside('-p 3000:3000 --user root') {
                withEnv(['NODE_OPTIONS=--max-old-space-size=2048 --openssl-legacy-provider']) {
                    sh '''
                        echo "🚀 Memulai proses build"
                        npm run build

                        # Opsional: Jalankan deploy script jika ada
                        if [ -f "./jenkins/scripts/deliver.sh" ]; then
                            chmod +x ./jenkins/scripts/deliver.sh
                            ./jenkins/scripts/deliver.sh
                        else
                            echo "⚠️ deliver.sh tidak ditemukan, melewati tahap deploy."
                        fi
                    '''
                }
            }
        }

    } catch (Exception e) {
        echo "❌ Pipeline gagal: ${e}"
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
	//echo 'Menunggu 1 menit agar aplikasi berjalan...'
        //sleep(time: 1, unit: 'MINUTES')
        echo '✅ Pipeline selesai.'
    }
} 

