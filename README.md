# Mini Project
## 1. Architecture
![1](https://github.com/user-attachments/assets/bef9c3aa-f615-4623-bd31-3037deb11c59)

## 2. Creating Domains on Route53 and ACM
![2](https://github.com/user-attachments/assets/24598ac5-29a0-40a0-8a71-e110305ad156)
### Register Domain on Route53

Search Route53 on AWS console page <br />
Click “Regitered Domains” in the bar  <br />
“Regiter Domain” to create a new domain  <br />
Enter the domain name that will be used (make sure the domain is available)(“Proceed to Checkout”)  <br />
Then determine how long the contract is according to the budget or rental year, also determine whether we want to automatically renew or not (“Next”)  <br />
Then fill in the appropriate domain orderer's personal data (“Next”)  <br />
Review and submit the domain order (“Submit”)  <br />

### Request Certificate in ACM

Search ACM (AWS certificate manager) on AWS console page  <br />
Click “Request” to request a new certificate  <br />
Select “Public certificate” <br />
Then fill in the domain name to be registered, also register the sub-domain by adding (*.) to the domain prefix  <br />
Use validation method (DNS / Email), and Select key algorithm (RSA, .etc) (“Request”)  <br />
Register CNAME for Validation by clicking “Create Records in Route53”  <br />
Check DNS in Route53 whether the CNAME was successfully added  <br />
Check again whether the certificate is valid and applied to DNS  <br />

## 3. Creating Security Groups for EC2, RDS, and ALB
### Architecture with OpenVPN and VPC Information
![3](https://github.com/user-attachments/assets/46c2b265-91bd-4c2f-bd13-a88547de9144)
#### Create a VPC and Subnet with a diagram as follows:

![Your paragraph text](https://github.com/user-attachments/assets/2487ba04-c423-42c7-872a-43d784b6a6c4)

***Create VPC***
Search VPC on the AWS console page <br />
Select "Create VPC" <br />
Resource to create : "VPC only" <br />
Enter a name VPC : "isw-vpc" <br />
Enter an IPv4 CIDR block : "10.1.0.0/16" <br />
"No IPv6 CIDR Block" In Ipv6 CIDR block <br />
Tenancy : "default" <br />
Select Create VPC
<br />
***Create Subnets***
Select Subnets on Bar VPC Resource <br />
Select "Create Subnet" <br />
VPC ID : "Use the VPC ID that we created previously" <br />
*Subnet 1 of 3*
Subnet name : "isw-public-subnet-a"<br />
Availability Zone : "select the one you want to use"<br />
IPv4 VPC CIDR block : "10.1.0.0/16"<br />
IPv4 subnet CIDR block : "10.1.1.0/26"<br />
<br />
Add new subnet <br />
*Subnet 2 of 3*
Subnet name : "isw-private-subnet-app-a"<br />
Availability Zone : "Select the same as public subnet"<br />
IPv4 VPC CIDR block : "10.1.0.0/16"<br />
IPv4 subnet CIDR block : "10.1.1.128/27"<br />
<br />
Add new subnet <br />
*Subnet 3 of 3*
Subnet name : "isw-private-subnet-app-a"<br />
Availability Zone : "Select the same as public subnet<br />
IPv4 VPC CIDR block : "10.1.0.0/16"<br />
IPv4 subnet CIDR block : "10.1.1.192/27"<br />
"Create Subnet" <br />
<br />
*Connect the public subnet to the internet gateway using route tables* <br />
*Connect the private subnet to the NAT gateway using route tables*<br />
#### Create OpenVPN for DevOps setup and access Private Resources


### Creating a Security Group
![4](https://github.com/user-attachments/assets/9a851805-a28f-4d59-b613-99a4a80fb548)

## 4. Create EC2, RDS, and ALB
![5](https://github.com/user-attachments/assets/8ff11ffc-3067-42f5-b1cf-5859ea2c77e8)

## 5. Creating a Simple Frontend (ReactJS + Nginx)
![6](https://github.com/user-attachments/assets/f36f7dcd-3aa1-438e-b6da-659d1d6a7fbf)

## 6. Creating a Simple Backend (Golang + Systemd + MySQL)
![7](https://github.com/user-attachments/assets/1964b165-ef8f-4c56-8325-ae6c4d2b38bc)

## 7. Cloudfront Integration with ALB
![8](https://github.com/user-attachments/assets/f70e8bdb-2b98-4eb0-8b91-2a8e63414a2b)

## 8. Review dan Test Aplikasi

## 9. CI/CD using Jenkins
![9](https://github.com/user-attachments/assets/ec18b291-ca44-49e4-aec0-ed844d2ad1cc)

## 10. Cloudfront Cache Invalidation
![10](https://github.com/user-attachments/assets/db244773-e760-416a-abaa-a1514fe0e16a)
