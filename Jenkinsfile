pipeline{
agent any
	
	
	environment {
    registry = "himanshu1018/finalcapstone"
    registryCredential = 'DOCKER_HUB_CREDENTIALS'
                     }
	tools { 
        maven 'Maven3'
        jdk 'jdk' 
               }
stages
{
stage('Compile')
{
   steps{ 
	  
	   withMaven(jdk: 'jdk', maven: 'Maven3'){
		   sh "mvn compile"
	   }
	   echo "Compilation Completed"
         }
 }		
				
stage('Testing')
{
   steps{
     
	    withMaven(jdk: 'jdk', maven: 'Maven3'){
		   sh "mvn test"
	   }
     echo "Testing Completed"
        }
}
stage('packaging')
     {
        when { 
	           anyOf { branch 'main'; branch 'development' }
              }

   steps{
	       withMaven(jdk: 'jdk', maven: 'Maven3'){
		   sh "mvn package"
	   }                                                                 
	}
}
stage('build image')
      {
         when{
                branch 'main'
              }
    //steps{ 
	   //    sh " docker build -t  himanshu1018/finalcapstone:$BUILD_NUMBER ."
          //}
	      steps{
                  script {
                     docker.build registry + ":$BUILD_NUMBER"
                          }
	            }
}


stage('push image')
     {
         when{
               branch 'main'
              }
     steps{
             withCredentials([string(credentialsId: 'DOCKER_HUB_CREDENTIALS', variable: 'DOCKER_HUB_CREDENTIALS')]) {
             echo "pushing the image to the dockerhub registry"
	         sh "docker login -u himanshu1018 -p ${DOCKER_HUB_CREDENTIALS}"
		     sh " docker push ${registry}:$BUILD_NUMBER"
			       }
	         sh 'docker rmi himanshu1018/finalcapstone:$BUILD_NUMBER'
             sh "echo ###########Local Image removed##########" 
           }
}	

stage('Deploy to K8')
   

    {
            when{
                 branch 'main'
                 }
			
        steps{
	
		      sh "pwd"
	              sh "kubectl apply -f kubernetes/namespace.yml"
                      sh "kubectl apply -f kubernetes/mysql-admin-secrets.yml"
	              sh "kubectl apply -f kubernetes/mysql-configmap.yml"
	              sh "kubectl apply -f kubernetes/mysql-user-secrets.yml"
	              sh "kubectl apply -f kubernetes/mysql-deployment.yml"
	            //sh "kubectl apply -f kubernetes/student-deployment.yml"
                  sh "cat kubernetes/student-deployment.yml | sed 's/finalcapstone:latest/finalcapstone:$BUILD_NUMBER/' |kubectl apply -f -"
		
		      } 
	    


	 }
	    }
  

            post {
		         always{       
		               mail to: "himanshu.upadhayay@knoldus.com",
                               subject: "Image build succesfully",
                                body: "Hello successfull completion of task, ${env.JOB_NAME} has been build successfully"
                        }


                  }  

 

  }

		
	   

