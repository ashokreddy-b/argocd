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
	}       
	stage('Run Docker Container'){
		steps{
			sh 'docker run -itd -p 8081:8081 bapathuashokreddy/javaproject'
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
