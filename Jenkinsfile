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
		 echo 'Testing Application completed'
           }
}
stage("SonarQube Analysis"){
	steps {
		script {
		  	withSonarQubeEnv(credentialsId: 'SonarQube') {
                   		sh "mvn sonar:sonar"
			}
		}
		 echo 'Analysis completed'
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
              sh "scp -o strictHostKeyChecking=no deployjava.yaml ubuntu@172.31.4.169:/home/ubuntu"
              script{
                  try{
                      sh "ssh ubuntu@172.31.4.169 kubectl apply -f deployjava.yaml"
                  }catch(error)
                  {
                      sh "ssh ubuntu@172.31.4.169 kubectl create -f deployjava.yaml"
                  }
              }
            }
        }
      }
  }
 post {
        success {
            emailext (
                subject: "Java Pipeline Status:: ${currentBuild.currentResult}",
                body: "The build status is: ${currentBuild.currentResult}",
                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
                to: "bapathu.ashokreddy@avinsystems.com" 
            )
        }
         failure {
            // This stage will always run, regardless of the build result
            emailext (
                subject: "Java Pipeline Status: ${currentBuild.currentResult}",
                body: "The build status is: ${currentBuild.currentResult}",
                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
            )
        }
    }
}
