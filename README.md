# AWS automation with Ansible

This playbook installs and configures a three tier architecture for creating a VPC in Amazon Web services. It contains a web layer, an application layer and a database layer. There are two EC2 instances running in two different availability zones in the web layer and the same for the application layer as well.

The playbook accepts inputs given by the user such as names, CIDR blocks, tags etc and then builds the VPC in AWS. This is still a work in progress. 

There is a main playbook called vpc_setup.yml from which all the different playbooks are called. The different playbooks accepts inputs from the user using "vars_prompt" and then stores the variables using "set_facts". After the  inputs have been registered the playbook calls the respective roles to create it's respective component of the VPC. The different playbooks are 
1) vpc.yml - This playbook creates the VPC with the inputs from the user as parameters.
2) igw.yml - This playbook is used to create the Internet Gateway.
3) subnet.yml - This playbook is used to create subnets inside the VPC. The web layer and application have two subnets each in two different availability zones and database layer has a subnet all of which are private. The load balancer has two subnets in the two availability zones which is same as the zones for web layer.
4) nat.yml - This is used to create a NAT gateway.
5) routetable.yml - This playbook is used to create the routetable for the different associated subnets in the VPC.
6) rds_subnet_group.yml - This playbook is used to create a RDS subnet group so that we can configure a VPC inside the VPC.
7) rds.yml -  This is used to create a RDS instance. It accepts inputs from the user to determine the type of RDS, instance, size, username etc and configures it accordingly.
8) security_group.yml - This playbook is used to create the security groups for the different subnets.
9) ec2.yml - This playbook is used to create EC2 instances for the web and application layer.
10) elb.yml -  This is used to create a load balancer to manage the traffic to the web layer.

The above mentioned playbooks will call their respective roles to execute the task and configure the differnet components of the VPC. 

## Running the playbook
Open the terminal and change the path to where you have downloaded the folder.
Then run the following command in the terminal:
> $ anisble_playbook vpc_setup.yml

## Requirements
You must have AWS CLI, latest Python module and boto3 installed before running this playbook else it will result in an error saying you need one of these installed.

If you don't have it installed follow the steps below in the terminal:

1) Install Homebrew with the following command

    > $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
2) Once you’ve installed Homebrew, insert the Homebrew directory at the top of your PATH environment variable. You can do this by adding the following line at the bottom of your ~/.profile file

    > export PATH=/usr/local/bin:/usr/local/sbin:$PATH
3) Now install Python with the following
    > $ brew install python
4) Install pip using
    > sudo easy_install pip
5) Install AWS CLI using pip

    >pip install awscli

6) Install boto3

    > brew install python