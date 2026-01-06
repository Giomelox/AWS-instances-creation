### Before starting, it is important to note that the AWS Region used is US East (Ohio) – us-east-2.

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



# Main Configuration
In this step, we will use four tools: 
1. Target Group
2. Security Groups
3. Load Balancers
4. Instances



## Target Group

This step is simple. First, set the target type to Instances and choose a name for your target group.

Set the protocol to 'HTTP'.

WARNING: This step is very important. You need to choose a port for your target group, and this port must match the listener port of your instance. In this example, we will use port 3001.
This configuration will be performed using PowerShell on our instance.

Select the IPv4 IP address type.

Select the previously created VPC

Select the 'HTTP1' protocol version

<img width="1042" height="654" alt="image" src="https://github.com/user-attachments/assets/7aec4bcb-607e-4637-bc92-827b44f96087" />

For the health checks and the remaining settings, leave the defaults.

You can go next and create the target group.



## Security Groups

### First, we will create a Security Group (SG) for our ELB (Elastic Load Balancer).

Choose a name for your Security Group, in this case 'ELB-security-group'.
Provide a description for this SG and select the previously created VPC.

In 'Inbound rules', add the following rules:

<img width="1359" height="390" alt="image" src="https://github.com/user-attachments/assets/847f3d2b-5507-41dd-8e9c-46c03ab53745" />

In 'Outbound rules', add a rule allowing all traffic:

Type: All traffic | Destination: Custom | IP Range: 0.0.0.0/0

### Now we will create a Security Group (SG) for our EC2 instance.

Choose a name for your Security Group, in this case 'main-security-group-ec2'.
Provide a description for this SG and select the previously created VPC.

In 'Inbound rules', add the following rules:

(For SSH, set the source to your IP address by selecting the My IP option)

(For Custom TCP, set the source to the previously created security group, 'ELB-security-group')

<img width="1340" height="296" alt="image" src="https://github.com/user-attachments/assets/7af768c9-2fc0-4ce5-8ab0-8ba9d68348ef" />

In 'Outbound rules', add a rule allowing all traffic:

Type: All traffic | Destination: Custom | IP Range: 0.0.0.0/0



## Load Balancer
Select the ELB type: Application Load Balancer and choose a name, in this case: 'main-ELB'

The scheme is set to 'internet-facing', and the IP address type is IPv4.

<img width="1304" height="505" alt="image" src="https://github.com/user-attachments/assets/31f84cf9-618d-4980-8b59-a6920cc39b43" />

Below, select the previously created VPC and choose the two public subnets.
Note that if a private subnet is selected, a warning about denied public access will appear. This occurs because only public subnets have a route to the Internet Gateway.

<img width="1314" height="608" alt="image" src="https://github.com/user-attachments/assets/b563b06e-c84e-4e57-b17b-755391b1323a" />

Now, choose the previously created security group, 'ELB-security-group'.

In 'Listeners and routing', set the protocol to 'HTTP' and the port to '80'.
Set the routing action to 'Forward to target group' and select the previously created target group.

And create your load balancer.



## Instances
I will not go into advanced details about this service; only the basic settings will be used to create the instance.

First, set a name for your instance, such as 'main-ec2-instance'. Next, choose an OS image. In this example, 'Amazon Linux' is used, which is eligible for the AWS Free Tier.

<img width="866" height="612" alt="image" src="https://github.com/user-attachments/assets/c1d533a6-7fbe-4fbd-ad09-6cb1908aaf1d" />

The instance type used is 't3.micro', which is eligible for the AWS Free Tier. You may use a different instance type, but be careful with the associated costs.

<img width="856" height="235" alt="image" src="https://github.com/user-attachments/assets/4430a709-ac55-4334-b206-277848ab5109" />

Select a key pair. If you do not have one, create a new key pair using the RSA key type and the .pem file format. 
!!! After creation, the key pair will be downloaded automatically. Make sure to store it securely, as it cannot be downloaded again. !!!

<img width="584" height="571" alt="image" src="https://github.com/user-attachments/assets/3fd6d534-61c4-4d1d-9a53-a27697dc4945" />

Now we will entry on network settings, click in 'Edit'

First select the previously created VPC and select a public subnet. Next, enable the auto-assign public IP.

Set the 'Select existing security group' and choose the 'main-security-group-ec2' SG.

<img width="867" height="601" alt="image" src="https://github.com/user-attachments/assets/3cd9e3b4-5569-4a07-b2b0-98be74b98558" />

And launch instance.



## Target Group

Go back to the Target Groups screen and select the target group you created.

Next, go to the Targets tab in the navigation bar and click 'Register targets'.

<img width="1117" height="542" alt="image" src="https://github.com/user-attachments/assets/8c353dc2-234b-4536-b9a8-9eeb2ae657b5" />

Select your instance, set the port to '3001', and mark it as pending.

<img width="1361" height="545" alt="image" src="https://github.com/user-attachments/assets/cad3daa8-fb03-494c-8a88-f2d3ee57cba2" />

Then, register the pending targets.

Wait a few seconds and check that the instance status is Healthy.

<img width="1130" height="358" alt="image" src="https://github.com/user-attachments/assets/e9a0a764-99e4-434f-b2d7-bd1d3b189a77" />





