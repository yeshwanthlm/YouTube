# Create custom Amazon Machine Images (AMI) from an EC2 instance

An Amazon Machine Image (AMI) contains all the information necessary to boot an Amazon EC2 instance with your custom software. 
Essentially, an AMI is a virtual machine template that can define custom software, standard system packages, or any files required by the virtual machine. 
Creating your own AMI is an essential practice if you plan to build an infrastructure that utilizes EC2 Auto Scaling Groups.

## Topics Covered:
•	Storage
•	Compute
•	High Availability
•	Amazon Web Services
•	Compute for AWS
•	Storage for AWS
•	Amazon Elastic Block Storage (EBS)
•	Amazon EC2

## Commands used in the video tutorial:

### Login as a root user: 
sudo su -

### Update the system: 
yum update -y

### Install Nginx:
yum install nginx -y

### Start Nginx Service:
sudo service nginx start 

### Status of Nginx Service:
systemctl status nginx.service
