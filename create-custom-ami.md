# Create custom Amazon Machine Images (AMI) from an EC2 instance

An Amazon Machine Image (AMI) contains all the information necessary to boot an Amazon EC2 instance with your custom software. 
Essentially, an AMI is a virtual machine template that can define custom software, standard system packages, or any files required by the virtual machine. 
Creating your own AMI is an essential practice if you plan to build an infrastructure that utilizes EC2 Auto Scaling Groups.

AWS Auto Scaling Groups require self-configurable instances in order to automatically scale up or down to match your application requirements. Your AMI serves as the basic unit of deployment, enabling the Auto Scaling Group to dynamically boot new custom instances as you need them.

AMIs can be backed by Amazon Elastic Block Storage (EBS) or instance store volumes. When an instance is launched using an EBS-backed AMI, the root volume for the instance is created using an EBS snapshot. When an instance is launched using an instance store-backed AMI, the root volume is created using a template stored in Amazon S3.

In this demo, you will create an AMI from a custom EC2 instance that has the Nginx web server preinstalled.


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

(If you are using Amazon Linuix 2 please use this command: sudo amazon-linux-extras install nginx1)

### Start Nginx Service:
sudo service nginx start 

### Status of Nginx Service:
systemctl status nginx.service
