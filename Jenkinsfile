pipeline {
    agent {
        docker {
            image 'node:latest' 
            args '-p 3000:3000' 
        }
    }
    stages {
        stage('Install Dependencies') { 
            steps {
                sh 'npm ci'
                sh 'npm run cy:verify'
            }
        }
        stage('Build') { 
            steps {
                sh 'npm run build'
            }
        }
       
    }
}
