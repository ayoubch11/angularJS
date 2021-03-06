pipeline {
    agent any
    environment {
        GIT_LATEST_COMMIT_EDITOR= sh(
            returnStdout:true,
            script: 'git show -s --pretty=%cn '
        ).trim()
        GIT_SSH_URL = sh(
            returnStdout: true,
            script: "git config --get remote.origin.url | sed 's/https:\\/\\/github.com\\//git@github.com:/g'"
        )
        GIT_CURRENT_BRANCH = sh(
            returnStdout: true,
            script: "git rev-parse --abbrev-ref HEAD"
        )
        HOME = '.'
       registry = "YourDockerhubAccount/YourRepository" 
        registryCredential = 'DockerHub' 
        dockerImage = 'ayoubch1/angular:${BUILD_ID}'
   
	  }


    stages {
        stage ('Show commit author') {
            steps {
                sh "echo '${env.GIT_LATEST_COMMIT_EDITOR}'"
            }
        }
        stage ('Execute CI pipeline') {
            agent {
                docker { image 'node:12-buster-slim' }
            }
            stages{
                stage ('npm install'){
                    steps {
                        sh "npm install"
                    }
                }
                stage('NPM build'){
                    steps {
                        sh 'npm run-script build --prod'
                    }
                }
                stage ('Artifacts') {
                    steps {
                        sh "tar czvf dist.tar.gz dist"
                        sh 'tar -czvf app.${BUILD_ID}.tar.gz dist'
                        archiveArtifacts "**/*.tar.gz"
                    }
                }
            }
        }
       stage('Build image') {         
     steps {
       sh 'docker build /var/lib/jenkins/workspace/angular@2/ -t ayoubch1/angular:${BUILD_ID}'
     }
       } 
      stage('Deploy Image') {
      steps{
       
        sh 'docker push ayoubch1/angular:${BUILD_ID}'
        sh 'docker tag ayoubch1/angular:${BUILD_ID} ayoubch1/angular:latest'
        
        
      }
    }
      
      stage('Remove Image & container') {
      steps{
       
        sh 'docker rmi -f ayoubch1/angular:${BUILD_ID}'
        sh 'docker stop angular || true && docker rm -f angular || true'
      }
    }
       stage('RUN Image') {
      steps{
       
        sh 'docker run --name=angular -it -d -p 80:80 ayoubch1/angular:latest'
        
      }
    }
    }
   
}
