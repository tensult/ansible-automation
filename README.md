# AWS automation with Ansible

This playbook installs and configures a three tier architecture for creating a VPC in Amazon Web services. It also sets up a VPN connection with the VPC. It contains a web layer, an application layer and a database layer. There are two EC2 instances running in two different availability zones in the web layer and the same for the application layer as well.

The playbook accepts inputs given by the user such as names, CIDR blocks, tags etc and then builds the VPC in AWS. This is still a work in progress. 

The main playbook I have created is "3tier_arch.yml" which calls other play-books to configure the different components. Each playbook calls the concerned role to execute the task and configure the required component. The different roles used are:
1) vpc - This role is used to create the VPC in the inital step. It takes parameters such as name of the VPC, the CIDR block, region etc as inputs from the user and then configures the VPC based on these inputs.

2) igw - This role is used to set-up the internet gateway for the VPC.

3) subnet - This role is used to create a subnet inside the VPC. The web layer and application have two subnets each in two different availability zones and database layer has a subnet all of which are private. The load balancer has two subnets in the two availability zones which is same as the zones for web layer. Different playbook are used to call this role which then creates a subnet based on the parameters passed to the role from the respective playbook. 

4) nat- This role is used to create a NAT gateway for theVPC.

5) routetable - This role is used to create the routetable for the different associated subnets in the VPC. The same process whereby we call the same role with different playbooks as in the case of "subnet" role is used to create different routetables for the different layers in the VPC.

6) rds_subnet_group - This role is used to create a RDS subnet group so that we can configure a realtional database service within the VPC.

7) rds- This role is used to create a RDS instance. The concerned playbook accepts inputs from the user to determine the type of RDS, instance, size, username etc which passes the inputs to the role so that it can configure it accordingly.

8) security_group- This role is used to create the security group for the different subnets. The different roles used to create security groups are app_security_group, db_security_group and wb_security_group. The playbooks associated with each role accepts the required inputs and passes it to the concerned role.

9) ec2 - This role is used to create EC2 instances for the web and application layer. As we have seen in the case of "subnet" roles, the different playbooks accept inputs from the user and then they call the "ec2" role which configures the required instance in the VPC.

10) elb- This role is used to create a load balancer to manage the traffic to the web layer.

11) cgw - This role is used to create a customer gateway for the VPN connection. The concerned playbook gathers the inputs from the user and then invokes the role.

12) vgw - This role is used to create a virtual private gateway and then attach it to the VPC.

13) vpn_connection - This role is used to set-up the virtual private network connection between the customer gateway and the virtual private gateway.

14) vpn_routetable - This role is used to create a routetable for the virtual gateway and also to allow route propogation for the virtual gateway.

The roles invoke the AWS modules in Ansible to carry out their respective tasks. 

## Running the playbook
Open the terminal and change the path to where you have downloaded the folder.
Then run the following command in the terminal:
> $ anisble_playbook 3tier_arch.yml

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