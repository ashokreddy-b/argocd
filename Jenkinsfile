pipeline {
  agent any
  stages {
 stage('Checkout'){
	 steps{
		 git 'https://github.com/ashokreddy-b/JavaInsure.git' 
	  }
  }
stage('Package and Build App') {
      steps {
        echo 'Build and Package App'
        sh 'mvn clean package'
           }
   	 }
   	  stage("Test Application"){
           steps {
                 sh "mvn test"
           }
}
stage("SonarQube Analysis"){
	steps {
		script {
		  	withSonarQubeEnv(credentialsId: 'SonarQube') {
                   		sh "mvn sonar:sonar"
			}
		}
	}
}
stage('Docker Image creation') {
      steps {
        	sh 'docker build -t bapathuashokreddy/javaproject .'
      }
}       
stage('Run Docker Container'){
	steps{
		sh 'docker run -itd -p 8081:8081 bapathuashokreddy/javaproject'
    	}
}
stage('deploy the K8S Cluster') {
        steps{
          sshagent(['sshCredentials']) {
              sh "scp -o strictHostKeyChecking=no deployjava.yaml ubuntu@172.31.32.226:/home/ubuntu"
              script{
                  try{
                      sh "ssh ubuntu@172.31.32.226 kubectl apply -f ."
                  }catch(error)
                  {
                      sh "ssh ubuntu@172.31.32.226 kubectl create -f ."
                  }
              }
            }
        }
      }
  }	
}
