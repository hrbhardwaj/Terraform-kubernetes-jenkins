# Terraform-kubernetes-jenkins

In this Project I used these tools :

* Github
* Dockerhub
* Terraform
* Jenkins
* Kubernetes

Step1 : Simple Install terraform on ec2 instance and launch three server by the help of terraform script .
<pre>
<code>
# Terraform File to Create instances 
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}
provider "aws" {
  region     = "us-west-1"
  access_key = "YOUR_ACCESS_KEY"
  secret_key = "YOUR_SECRET_KEY"
}
resource "aws_instance" "master_instance" {
  count         = 1
  ami           = "ami-0c55b159cbfafe1f0"  # Change to your desired AMI ID
  instance_type = "t2.medium"

  tags = {
    Name = "kubemaster"
  }
}
resource "aws_instance" "slave_instance" {
  count         = 2
  ami           = "ami-0c55b159cbfafe1f0"  # Change to your desired AMI ID
  instance_type = "t2.medium"

  tags = {
    Name = "kubeslave"
  }
}
# chnage the ami and access & secret key.
 '''
</code>
</pre>

Step2 : Install jenkins and create a jenkins and node cluster with kube-master server
<pre>
<code>
# Jenkins pipeline to deployment the jobs(website) on slave 

  pipeline {
    agent none
    environment {
        DOCKERHUB_CREDENTIALS = credentials('0519a98d-69e6-44b2-89fd-c2f9e2a6308e')
        GIT_CREDENTIALS = credentials('ba8db134-f2e0-44d7-b91d-edc48463ca1c')
    }
    stages {
        stage('Hello world') {
            agent {
                label 'kubemaster'
            }
            steps {
                echo 'I am testing my script'
            }
        }
        stage('gitCheckout') {
            agent {
                label 'kubemaster'
            }            
            steps {
                git branch: 'main', credentialsId: 'ba8db134-f2e0-44d7-b91d-edc48463ca1c', url: 'https://github.com/hrbhardwaj/Terraform-kubernetes-jenkins.git'
            }
        }
        stage('Dockerhub') {
            agent {
                label 'kubemaster'
            }        
            steps {
                sh 'sudo docker build /home/ubuntu/workspace/pipeline -t hrithikbhadwaj/website2 '
                sh "echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
                sh 'sudo docker push hrithikbhadwaj/website2'
            }
        }        
        stage('deployment') {
            agent {
                label 'kubemaster'
            }        
            steps {
                sh 'sudo kubectl apply -f /home/ubuntu/workspace/pipeline/deploy.yml'
                sh 'sudo kubectl apply -f /home/ubuntu/workspace/pipeline/svc.yml'
            }
        }
    }
}  
</code>
</pre>
Step3 : Now create a pipeline for running four jobs with the pipeline 

  *  Print hello world    

  *  Clone the repository to Github    

  *  Build the docker images from docker file and push to docker-hub   

  *  Apply the Deplyoment manifest and Service minifest on Kube-master


<img width="1374" alt="Screenshot 2023-08-26 at 8 20 44 PM" src="https://github.com/hrbhardwaj/Terraform-kubernetes-jenkins/assets/131919525/6bd45433-4cea-469c-9dbd-522a44c2b6e7">


Now you can Check the website with public Ip with Nordport !!!

Slav1 IP with Nordport


<img width="1440" alt="Screenshot 2023-08-27 at 3 19 04 AM" src="https://github.com/hrbhardwaj/Terraform-kubernetes-jenkins/assets/131919525/fefa3a9b-91b5-4b9b-a260-affb4c2548a3">



Slav2 Ip with NordPort


<img width="1415" alt="Screenshot 2023-08-27 at 3 19 21 AM" src="https://github.com/hrbhardwaj/Terraform-kubernetes-jenkins/assets/131919525/494e2078-de1f-437e-97d4-c9efffeedd2d">




It will visible by all worker node IP









  



