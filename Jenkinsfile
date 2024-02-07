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
	stage('ECR login') {
	      steps {
	        	sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 062813490047.dkr.ecr.ap-south-1.amazonaws.com"
	      }
	}  
	stage('Docker Image creation') {
	      steps {
	        	sh "docker build -t ashokreddy ."
	      }
	}       
	stage('Taggong'){
		steps{
			sh "docker tag ashokreddy:latest 062813490047.dkr.ecr.ap-south-1.amazonaws.com/ashokreddy:latest"
	    	}
	}
	  stage('pushing'){
		steps{
			sh "docker push 062813490047.dkr.ecr.ap-south-1.amazonaws.com/ashokreddy:latest"
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
             emailext (
                subject: "Java Pipeline Status:: ${currentBuild.currentResult}",
                body: "The build status is: ${currentBuild.currentResult}",
                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
                to: "bapathu.ashokreddy@avinsystems.com" 
             )
         }
    }
}
