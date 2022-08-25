# FreePBXAWS
FreePBX project on AWS running on Ubuntu
FreePBX is an open-source community GUI that manages Asterisk VoIP PBX. In this project I installed and setup Freepbx 
onto an EC2 instance as well as setting up MYSQL and SMTP alongside this.

# Introduction/creating the keypair
Firstly I subscribed to Freepbx via aws marketplace, then I created a key pair, this is needed in order to access
the freepbx instance to connect to it via ssh. I did this by going to EC2 > key pairs > create key pair using key pair 
type RSA and private file key format as .pem

# Creating the EC2 instance 
The second step was to create the ec2 instance, this was done by going to ec2 > instances > launch instances. I then went to AWS marketplace 
within the instance and searched for freepbx. I chose t2.micro as the instance type created a new security groupe allowing traffic inbound from all IP/ports 
in order to ssh into the ec2 instance without any disruptions. 

# Acessing instance using cmd 
The next step was to access the ec2 instance using cmd, I ran cmd as adminsitartor 
