pipeline {
  agent any
	environment {
        AWS_DEFAULT_REGION = "ap-south-1" // Replace with your AWS region
        AWS_ACCOUNT_ID = "062813490047" // Replace with your AWS account ID
        ECR_REPO = "ashokreddy" // Replace with your ECR repository name
        IMAGE_TAG = "latest" // Replace with your desired image tag
	REPOSITORY_URL = "062813490047.dkr.ecr.ap-south-1.amazonaws.com/ashokreddy"
    }
  stages {
	 stage('Checkout'){
		 steps{
			 git 'https://github.com/ashokreddy-b/JavaInsure.git' 
		  }
		 post {
		 failure {
			  // This stage will always run, regardless of the build result
		          emailext (
		        subject: "Checkout stage: ${currentBuild.currentResult}",
	                body: "The Checkout status is: ${currentBuild.currentResult}",
	                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
	                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
	            )
	      }
	    }

	  }
	stage('Package and Build App') {
	      steps {
	        echo 'Build and Package App'
	        sh 'mvn clean package'
	        }
		post {
		 failure {
			  // This stage will always run, regardless of the build result
		          emailext (
		        subject: "packageing application stage: ${currentBuild.currentResult}",
	                body: "The packageing application status is: ${currentBuild.currentResult}",
	                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
	                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
	            )
	      }
	    }

	   }
	stage("Test Application"){
	           steps {
	                 sh "mvn test"
			 echo 'Testing Application completed'
	           }
		post {
		 failure {
			  // This stage will always run, regardless of the build result
		          emailext (
		        subject: "Test Application stage: ${currentBuild.currentResult}",
	                body: "The Test Application status is: ${currentBuild.currentResult}",
	                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
	                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
	            )
	      }
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
		post {
		 failure {
			  // This stage will always run, regardless of the build result
		          emailext (
		        subject: "SonarQube Analysis stage: ${currentBuild.currentResult}",
	                body: "The SonarQube Analysis status is: ${currentBuild.currentResult}",
	                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
	                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
	            )
	      }
	    }

	}
	
	stage('Docker Image creation') {
	      steps {
	        	sh 'docker build -t ashokreddy .'
	      }
		post {
		 failure {
			  // This stage will always run, regardless of the build result
		          emailext (
		        subject: "Docker Image creation stage: ${currentBuild.currentResult}",
	                body: "The Docker Image creation status is: ${currentBuild.currentResult}",
	                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
	                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
	            )
	      }
	    }

	}       
	stage('Run Docker Container'){
		steps{
			sh 'docker run -itd -p 8081:8081 ashokreddy'
	    	}
		post {
		 failure {
			  // This stage will always run, regardless of the build result
		          emailext (
		        subject: "Run Docker Container stage: ${currentBuild.currentResult}",
	                body: "The Run Docker Container status is: ${currentBuild.currentResult}",
	                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
	                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
	            )
	      }
	    }
	}
	stage('Push to ECR') {
            steps {
                script {
                    // Login to ECR registry
                    sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                    
                    // Tag Docker image with ECR repository
                    docker.image("your-docker-image-name:${IMAGE_TAG}").tag("${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}")
                    
                    // Push Docker image to ECR
                    docker.image("${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}").push()
                }
            }
	post {
		 failure {
			  // This stage will always run, regardless of the build result
		          emailext (
		        subject: "Push to ECR stage: ${currentBuild.currentResult}",
	                body: "The Docker Image creation status is: ${currentBuild.currentResult}",
	                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
	                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
	            )
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
    }
}
