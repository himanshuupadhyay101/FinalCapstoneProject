pipeline{
agent any
	
stages
{
stage('Compile')
{
   steps{ 
	  sh "mvn clean compile"
	   echo "Compilation Completed"
         }
 }		
				
stage('Testing')
{
   steps{
     sh "mvn test"
     echo "Testing Completed"
        }
}
stage('packaging')
{when { 
	anyOf { branch 'main'; branch 'development' }
}

   steps{
	sh "mvn package"                                                                  //sbt package vs sbt assembly
	}
}
stage('build image')
{
when{
branch 'main'
     }
    steps{ 
	 sh " docker build -t  himanshu1018/finalcapstone:$BUILD_NUMBER ."
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
          sh " docker push himanshu1018/finalcapstone:$BUILD_NUMBER"
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
	//kubernetesDeploy(
	   // configs: 'deploy.yml',
            // kubeconfigId: 'KUBERNETES_CLUSTER_CONFIG',
	    // enableConfigSubstitution: true
		//	  )
		sh "pwd"
	sh "kubectl apply -f kubernetes/namespace.yml"
        sh "kubectl apply -f kubernetes/mysql-admin-secrets.yml"
	sh "kubectl apply -f kubernetes/mysql-configmap.yml"
	sh "kubectl apply -f kubernetes/mysql-user-secrets.yml"
	sh "kubectl apply -f kubernetes/mysql-deployment.yml"
	//sh "kubectl apply -f kubernetes/student-deployment.yml"
        sh "cat kubernetes/student-deployment.yml | sed 's/finalcapstone:latest/finalcapstone:$BUILD_NUMBER/' |kubectl apply -f -"
		
		}
            post {
		    always{       mail to: "himanshu.upadhayay@knoldus.com",
                     subject: "Image build succesfully",
                     body: "Hello successfull completion f task, ${env.JOB_NAME} has been build successfully"
                  }
	    }
	    }
  }

		
	   
}
