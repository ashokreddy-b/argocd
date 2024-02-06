pipeline {
  agent any
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
	stage("Quality Gate"){
		steps{
			timeout(time: 5, unit: 'MINUTES') {
   			 script {
   			     def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv

    			    if (qg.status != 'OK') {
           			 error "Quality Gate check failed."
       			 } else {
           		 echo "Quality Gate check passed."
       			 }
    			}
			}	
		}
		post {
		 failure {
			  // This stage will always run, regardless of the build result
		          emailext (
		        subject: "Quality Gate stage: ${currentBuild.currentResult}",
	                body: "The Quality gate status is: ${currentBuild.currentResult}",
	                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
	                to: "bapathu.ashokreddy@avinsystems.com"  // Replace with the recipient's email address
	            )
	      }
	    }

	}
	stage('Docker Image creation') {
	      steps {
	        	sh 'docker build -t bapathuashokreddy/javaproject .'
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
			sh 'docker run -itd -p 8081:8081 bapathuashokreddy/javaproject'
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
