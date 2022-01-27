pipeline {
    environment {
    imagename = "ramisetty32/springboot"
    DOCKERHUB_CREDENTIALS=credentials('dockerhub')
   
  }
   agent any
   
   stages {
       stage('checkout') {
            steps {
                echo "this is checkout stage"
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ramisetty1/springboot.git']]])
            }
        }
        
        stage('Build'){
            steps{
                echo "building the code"
                sh 'mvn clean package'
	            }
        }
        
        stage('Docker Image Build'){
            steps{
                script{
                    docker.build imagename + ":$BUILD_NUMBER"
                }
	        }
        }

        stage('Docker Login') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Upload image') {

			steps {
				sh 'docker push ramisetty32/springboot' + ":$BUILD_NUMBER"
			}
		}
	stage('Trigger ManifestUpdate') {

			steps {
				echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
			}
		}   
	   
	
    }
    
}
 
