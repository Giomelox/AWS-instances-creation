# Initial Configuration


## VPC
I created a VPC with a manually defined IPv4 CIDR block set to 10.0.0.0/24.
<img width="985" height="386" alt="image" src="https://github.com/user-attachments/assets/4474d5f5-5307-4b9a-9005-51cf11cf8bb4" />

Next, I chose the option that does not include an IPv6 CIDR block and selected Default Tenancy.
<img width="1328" height="555" alt="image" src="https://github.com/user-attachments/assets/738f3a89-7578-491c-bcf7-0c6fd9b3f17f" />


## Subnets
In this step, I created four subnets: two public and two private, each in a distinct Availability Zone.
<img width="1171" height="651" alt="image" src="https://github.com/user-attachments/assets/57ee3710-721d-49d3-8807-8d6e8e64304c" />

The main difference between the subnets is the IPv4 CIDR block. The CIDR blocks used are listed below:

1. Public 2b: 10.0.0.128/26
2. Private 2b: 10.0.0.192/26
3. Public 2a: 10.0.0.0/26
4. Private 2a: 10.0.0.64/26

##  Internet Gateway + Route tables
In this step, I first created an internet gateway and, under Actions, attached it to the VPC.

Next, I created two route tables: one public and one private. In the public route table, I edited the routes to include the previously created Internet Gateway with the destination 0.0.0.0/0.

I did not modify the private route table because my EC2 instances are located in a public subnet.
