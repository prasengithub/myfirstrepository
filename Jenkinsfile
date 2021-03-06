pipeline {
    agent any
    stages {

        stage('Clone Repo') {
          steps {
            sh 'rm -rf packer-terraform-jenkins-docker'
            sh 'git clone  git@github.com:prasengithub/Packer-Terraform-Docker-Jenkins.git'
            }
        }
		
		stage('Packer Build AMI') {
          steps {
            
            sh 'pwd'
            sh 'ls -al'
            sh 'cp /var/lib/jenkins/workspace/pipeline2/packer-terraform-docker-jenkins/*.* .'
            sh 'ls -al'
            sh 'packer build packer.json'
            }
        }
				
		stage('Deploy EC2 Server') {
          steps {
		    sh 'terraform init'
            sh 'terraform apply --auto-approve'
            }
        }

        stage('Build Docker Image') {
          steps {
            sh 'cd /var/lib/jenkins/workspace/pipeline2/packer-terraform-docker-jenkins'
            sh 'cp  /var/lib/jenkins/workspace/pipeline2/packer-terraform-docker-jenkins/* /var/lib/jenkins/workspace/pipeline2'
            sh 'docker build -t prasengithub/DockerImage:${BUILD_NUMBER} .'
            }
        }

        stage('Push Image to Docker Hub') {
          steps {
           sh    'docker push prasengithub/DockerImage:${BUILD_NUMBER}'
           }
        }

        stage('Deploy to Docker Host') {
          steps {
		    sh 'sleep 10s'
            sh    'docker -H tcp://10.1.1.111:2375 stop prodwebapp1 || true'
            sh    'docker -H tcp://10.1.1.111:2375 run --rm -dit --name prodwebapp1 --hostname prodwebapp1 -p 8000:80 prasengithub/DockerImage:${BUILD_NUMBER}'
            }
        }

        stage('Check WebApp Rechability') {
          steps {
          sh 'sleep 10s'
          sh ' curl http://10.1.1.111:8000'
          }
        }

    }
}
