pipeline {
  agent any
  stages {
	  stage('Checkout'){
		  steps{
			  
		  }
	  }
   stage('Package and Build App') {
      steps {
        echo 'Build and Package App'
        sh 'mvn clean package'
           }
   	 }

stage('Image creation') {
      steps {
        sh 'docker build -t bapathuashokreddy/health-care021:latest .'
                    }
            }

stage('Docker Login') {
      steps {
	  withCredentials([usernamePassword(credentialsId: 'Docker', passwordVariable: 'pwd', usernameVariable: 'user')]) {
      sh "docker login -u ${env.user} -p ${env.pwd}"
}	
            }
      }

 stage('Push Image to Docker Registry') {
      steps {
        sh 'docker push bapathuashokreddy/health-care021:latest'
            }
           }
   stage('Run Container'){
	steps{
		sh 'docker run -itd -p 8084:8082 bapathuashokreddy/health-care021:latest'
	}
	}
}
}
