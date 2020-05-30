pipeline {
	agent {
		docker {
			image 'maven:3-alpine'
			args '-v /root/.m2:/root/.m2'
		}

	}
	options {
       		 skipStagesAfterUnstable()
    	}	

	stages {

		def commit_id
   		stage('Preparation') {
     			checkout scm
		        sh "git rev-parse --short HEAD > .git/commit-id"                        
     			commit_id = readFile('.git/commit-id').trim()
		}
		stage ('maven build') {
			steps {
				sh 'mvn -B -DskipTests clean package'
			}	
		}

		stage ('test') { 
			steps { 
				sh 'mvn test'
			}
			post { 
				always { 
					junit 'target/surefire-reports/*.xml'
				}
			}
		}
		stage ('deliver') { 
			steps { 
				sh './jenkins/scripts/deliver.sh'
			}
		}
		stage('docker build/push') {
    				 docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
      				 def app = docker.build("nancyrheniusbenny/docker-nodejs-demo:${commit_id}", '.').push()
     					
   			}
		}
	}
}
