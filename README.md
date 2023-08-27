# Terraform-kubernetes-jenkins

In this Project I used these tools :

* Github
* Dockerhub
* Terraform
* Jenkins
* Kubernetes

Step1 : Simple Install terraform on ec2 instance and launch three server by the help of terraform script .

...
   bash

   terraform
     required providers {
       aws = {
        source = "hashicorp/aws" 
        }
    }
}
provider "aws" {
  region     = "us-west-1"
  access_key = " "
  secret_key = " "
    
}
resource "aws_instance" "example_instance" {
  count         = 1
  ami           = "ami-0c55b159cbfafe1f0"  # Change to your desired AMI ID
  instance_type = "t2.medium"
  
  tags = {
    Name = "kubemaster"

}
resource "aws_instance" "example_instance" {
  count         = 2
  ami           = "ami-0c55b159cbfafe1f0"  # Change to your desired AMI ID
  instance_type = "t2.medium"
  
  tags = {
    Name = "kubeslave"



}





...
