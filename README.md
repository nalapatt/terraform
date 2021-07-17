# terraform to create ec2 instances



create an ec2 instance 
ssh into the ec2 instance

security credentials in account 
create access key
download access key - downloaded as csv file
show access key 


# Install terraform 
wget https://releases.hashicorp.com/terraform/1.0.2/terraform_1.0.2_linux_amd64.zip
unzip terraform_1.0.2_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform --version

# First setup your access keys, secret keys and region code locally.

aws configure

access_key = "AKIARJUQZC26BPG4K", ( copy from access key that is shown) 
secret_key = "Ow/xqgNgZWxJuQatqDNzLn+1FFMwe4wCD", ( copy from secret key shown)
region = "us-west-2"


# create a terraform file 
vi variable.tf

variable "aws_region" {
  description = "The AWS region to create things in."
  default     = "us-west-2"
}

variable "key_name" {
  description = " SSH keys to connect to ec2 instance"
  default     =  "aws-new-project-key"
}

variable "instance_type" {
  description = "instance type for ec2"
  default     =  "t2.micro"
}
                    



# create a main file
sudo vi main.tf

provider "aws" {
  region = var.aws_region
}


#Create security group with firewall rules
resource "aws_security_group" "security_jenkins_port" {
  name        = "security_jenkins_port"
  description = "security group for jenkins"

  ingress {
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

 ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

 # outbound from jenkis server
  egress {
    from_port   = 0
    to_port     = 65535
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags= {
    Name = "security_jenkins_port"
  }
}

resource "aws_instance" "myFirstInstance" {
  ami           = "ami-0dc8f589abe99f538 "
  key_name = aws-new-project-key.pem
  instance_type = var.instance_type
  security_groups= [ "security_jenkins_port"]
  tags= {
    Name = "jenkins_instance"
  }
}

# Create Elastic IP address
resource "aws_eip" "myFirstInstance" {
  vpc      = true
  instance = aws_instance.myFirstInstance.id
tags= {
    Name = "jenkins_elstic_ip"
  }
}

# save file 
esc :wq ( to save and exit)

# initialize terraform
terraform init

# terraform plan
terraform plan

# terraform apply

