================== INTRODUCTION ==================

This Project is based on an Automation tool called Ansible, by using this tool we will be creating entire stack of Web Application

and VPC in AWS, including subnets, internet gateway, route tables etc, whole process will be automatic through Ansible

Ansible playbooks will be run on EC2 server which will have role attach to it and python boto3 will be used for AWS API calls

==================SCENARIO====================

Before any project management team need to build & setup up an infrastructure on the cloud which requires

 HA and secure VPC and sometimes companies have multiple projects which need different VPC's with certain requirements 

 and regular requests

================== PROBLEMS ======================

Creation of VPC involves many steps and has many moving parts such as creating an internet gateway and attaching to it VPC, having 

bastion host, attaching EIP to ENI etc 

Human error can lead to non functional or expose VPC while creating all these components manually 

Creating VPC with all the required parts is a time consuming task

Complete infrastructure setup is complex and not repeatable with regular changing 

================== SOLUTIONS ======================

Configuration management of VPC with no human error with automatic setup so misconfiguration will happen 

In need to any change we will have centralize management system 

We will be using IAAC infrastructure as a code which gives the version control access less time consuming 

================== Architectural Design ==========
![Alt Text](https://github.com/aleem632/ansible-aws-vpc/blob/36bde73a996a74398ebbe39b48aea8786e59f14a/architectural-design/ansible-vpc-creation.png)



================== Flow Of Execution ============

1: Login to AWS console and choose IAM, Create a role for EC2 instance which will have Full adminstrator access but we can

 choose role according to project requirements, give the name to role and create it 

Create EC2 instance, choose Ubuntu 2004 focal, select keypair, default vpc, create SG if not created
 
attach the role to IAM instance profile 

USER DATA "#!/bin/bash"

sudo apt update 
sudo apt install ansible -y

launch the instance, SSH into instance and check for ansible version 

Install required dependencies usually ansible modules need python3-boto3

For TESTING purpose creat playbook with ec2 key creation module to check if everyhing is working fine

2: Create two variable files one is for VPC and one is bastion hostFor bastion host we will use secure AMI "CIS Redhat Enterprise Linux 7"

VPC variable file will have VPC name, CIDR range, three public, three private subnets and region

bastin host file will have AMI number and region and IP address of machine which will access bastion host 

3: Create ansible playbook which will create cidrrange of 172.20.0.0/16 

Write playbook to create VPC and three public subnet & private with range 251 IP address [172.20.0.0/24] 

because AWS will have some IP for other resources

After subnet and vpc creation we will create internet gate and route table with association of 3 public subnets

create an entry of 0000/0 to IGW 

Next step will be to create a NAT gateway for private subnets and route table and create a entry for 0000/0

 to NAT gateway so it can access internet but the creation of nat gateway will be in public subnet 

Debug module print all the variables and we will use set_fact module to set their value to new variables

then copy module will be used to copy all the variable and save into output_vars file for later use

4: Bastion playbook will have include_var module and we will have two variable files 

we will create key and add private key content in bastion_keypem file , Security group for bastion host with inbound traffic 

of port:22 MYIP 

Bastion host will be created using the key and security group created earlier and subnetnetid 

========================== Second Phase ==========================================

Architectural Design 
5: After the successful execution of VPC with public & private subnet and bastion host 

lets create another branch for next phase with same code 'git checkout -b javaapp'

Copy the AMI of bastion host from bastion_setup file to vpc_setup file so we dont have use different files

We will create another playbook which will have import_playbook module, this module will import both playbooks into 

this new playbook and will run it 

If already have vpc then no need to run that playbook but if we need to create new vpc then we can run that playbook

this playbook will create new varibles which will be saved in output_vars file and we will include that output_vars 

file in our bastion host playbook 

Bastion host private key is saved in bastion_keypem file which will be used for SSH 

6: Next step will be getting AMI of our backend and frontend EC2 instance we will be using ubuntu 20 focal in the region US-east-2

Find the latest AMI on AWS console and save in file Images for later reference in our ec2-stack playbook

Create ec2-stack playbook : First module is "include_vars" to add two variable files output_vars and Images file

Second step will be to create key for our web stack instance and save its private key 

DONT FORGET TO USE THIS CONDITION  when: keyoutchanged

Create Security group for load balancer which will allow traffic at port:80

Second Security group is for webstack which will have inbound traffic rule port:80 and Port:22 

From Bastion host SG and third rule: all traffic from its own SG group 









 

    





    


