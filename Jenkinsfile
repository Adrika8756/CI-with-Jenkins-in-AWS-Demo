pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'devops-Pradipta'
                CLUSTER_NAME = 'k8-cluster-demo' 
                LOCATION = 'europe-west3-c' 
                CREDENTIALS_ID = 'K8' 
	}
	
    stages {	
	   stage('Scm Checkout') {            
		steps {
                  checkout scm
		}	
           }
           
	   stage('Build') { 
                steps {
                  echo "Cleaning and packaging..."
                  sh 'mvn clean package'		
                }
           }
	   stage('Test') { 
		steps {
	          echo "Testing..."
		  sh 'mvn test'
		}
	   }
	   stage('Build Docker Image') { 
		steps {
                   script {
		      myimage = docker.build("gcr.io/devops-Pradipta/pradipta18031990/dockerimagelearning:${env.BUILD_ID}")
                   }
                }
	   }
	   stage("Push Docker Image") {
                steps {
                   script {
                      docker.withRegistry('https://gcr.io') {
                            myimage.push("${env.BUILD_ID}")		
                     }
			   
                   }
                }
            }
	   
           stage('Deploy to K8s') { 
                steps{
                   echo "Deployment started ..."
		   sh 'ls -ltr'
		   sh 'pwd'
		   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                   step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
		   echo "Deployment Finished ..."
            }
          }
    }
}
