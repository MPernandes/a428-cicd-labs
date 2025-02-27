pipeline {
    agent {
        docker {
            image 'node:lts-buster-slim'
	    args '-u root'	   
		// args '--user $(id -u):$(id -g)'
            //args '--memory=850m --memory-swap=3g --cpus=1 --user root -p 3000:3000'
        }
    }
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                //sh 'node --max-old-space-size=4096 $(which npm) install'
                sh '''
		export NODE_OPTIONS="--max-old-space-size=4096"
		rm -rf node_modules package-lock.json
         	npm cache clean --force 
		npm install
                npm install @babel/core@^7.22.0 @babel/preset-env@latest --save-dev
		'''
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the website? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
