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
<br />

![Your paragraph text](https://github.com/user-attachments/assets/2487ba04-c423-42c7-872a-43d784b6a6c4)

***Create VPC*** <br />
<br />
Search VPC on the AWS console page <br />
Select "Create VPC" <br />
Resource to create : "VPC only" <br />
Enter a name VPC : "isw-vpc" <br />
Enter an IPv4 CIDR block : "10.1.0.0/16" <br />
"No IPv6 CIDR Block" In Ipv6 CIDR block <br />
Tenancy : "default" <br />
Select Create VPC <br />
<br />
***Create Subnets*** <br />
<br />
Select Subnets on Bar VPC Resource <br />
Select "Create Subnet" <br />
VPC ID : "Use the VPC ID that we created previously" <br />
*Subnet 1 of 3*
<br />
Subnet name : "isw-public-subnet-a"<br />
Availability Zone : "select the one you want to use"<br />
IPv4 VPC CIDR block : "10.1.0.0/16"<br />
IPv4 subnet CIDR block : "10.1.1.0/26"<br />
<br />
Add new subnet <br />
<br />
*Subnet 2 of 3*
<br />
Subnet name : "isw-private-subnet-app-a"<br />
Availability Zone : "Select the same as public subnet"<br />
IPv4 VPC CIDR block : "10.1.0.0/16"<br />
IPv4 subnet CIDR block : "10.1.1.128/27"<br />
<br />
Add new subnet <br />
<br />
*Subnet 3 of 3*
<br />
Subnet name : "isw-private-subnet-app-a"<br />
Availability Zone : "Select the same as public subnet<br />
IPv4 VPC CIDR block : "10.1.0.0/16"<br />
IPv4 subnet CIDR block : "10.1.1.192/27"<br />
"Create Subnet" <br />
<br />
*Connect the public subnet to the internet gateway using route tables* <br />
*Connect the private subnet to the NAT gateway using route tables*<br />
<br />
#### Create OpenVPN for DevOps setup and access Private Resources
<br />
Search EC2 Instance on AWS console page  <br />
Click “Launch Instances” to create new instance  <br />
Name : "isw-openvpn" <br />
Select "Browse more AMIs"<br />
Search "Openvpn" on search bar <br />
Select "OpenVPN Acces server" <br />
Click "Subcribe now" <br />
Instance type : t2.micro <br />
Key pair : "Create new key pair"<br />
<br />
*Make sure you store the key pair safely*<br />
<br />
Network settings:<br />
 1. VPC : Use the VPC that we created previously <br />
 2. Subnet : Use the Public-subnet<br />
 3. Auto-assign public IP : Enable <br />
 4. Security group name : Replace name with "my-openvpn-sg" <br />
 5. Description : default<br />
 6. Inbound security group rules: default <br />
"Launh Instance" <br />
<br />

**Setup OpenVpn** <br />

<br />
Login to openvpn
<br />

```

$ssh -i keypair.pem root@ipOpenvpn

```

<br />
follow the setup instructions<br />
<br />
*Try to log in to the admin page with the username and password that we specified during setup*<br />
<br />
ipOpenvpn:943/admin (for Admin) <br />
ipOpenvpn:943 (for Client)<br />

### Creating a Security Group
![4](https://github.com/user-attachments/assets/9a851805-a28f-4d59-b613-99a4a80fb548)
<br />
Search Security Group on AWS console page <br />
Click "Create Security group" to create new security group <br />
<br />
***Security Group LoadBalancer*** <br />
<br />
Name : my-app-alb-sg <br />
Description : my-app-alb-sg <br />
VPC : Use the VPC that we created previously <br />
*Inbound rules* <br />
Add rules according to the diagram : <br />
<br />
1.Type : HTTP, Port : 80, Source : Anywhere IPv4 (0.0.0.0/0) <br />
2.Type : HTTPS, Port : 443, Source : Anywhwre IPv4 (0.0.0.0/0) <br />
<br />
*Outbound rules* <br />
Default (0.0.0.0/0) <br />
Add tag Name : my-app-alb-sg <br />
"Create Security Group" <br />
<br />
***Security Group App Ec2*** <br />
<br />
Name : my-app-ec2-sg <br />
Description : my-app-ec2-sg <br />
VPC : Use the VPC that we created previously <br />
*Inbound rules* <br />
Add rules according to the diagram : <br />
<br />
1.Type : SSH, Port : 22, Source : my-openvpn-sg <br />
2.Type : Custom TCP, Port : 3000, Source : my-openvpn-sg <br />
3.Type : Custom TCP, Port : 8080, Source : my-openvpn-sg <br />
4.Type : Custom TCP, Port : 8080, Source : my-app-alb-sg <br />
5.Type : HTTP, Port : 80, Source : my-app-alb-sg <br />
6.Type : HTTPS, Port : 443, Source : my-app-alb-sg <br />
<br />
*Outbound rules* <br />
Default (0.0.0.0/0) <br />
Add tag Name : my-app-ec2-sg <br /> 
"Create Security Group" <br />
<br />
***Security Group App Database*** <br />
<br />
Name : my-app-rds-sg <br />
Description : my-app-rds-sg <br />
VPC : Use the VPC that we created previously <br />
*Inbound rules* <br />
Add rules according to the diagram : <br />
<br />
1.Type : MYSQL, Port : 3306, Source : my-openvpn-sg <br />
2.Type : MYSQL, Port : 3306, Source : my-app-alb-sg <br />
<br />
*Outbound rules* <br />
Default (0.0.0.0/0) <br />
Add tag Name : my-app-rds-sg <br /> 
"Create Security Group" <br />
<br />


## 4. Create EC2, RDS, and ALB
![5](https://github.com/user-attachments/assets/8ff11ffc-3067-42f5-b1cf-5859ea2c77e8)
<br />
### Creating EC2
Search EC2 on AWS console page <br />
Click "Launch Instances" to create new EC2 instance <br />
Name and tag = my-app-ec2 <br />
Applications and OS image (AMI) =  Amazon Linux <br />
Instance type = t3.micro <br />
Keypair = keypair that we have created <br />
<br />
*Network Settings* <br />
VPC = isw-vpc <br />
Subnet = isw-private-subnet-a <br />
Auto-assign public ip = Disable <br />
Firewall = Select existing security group (my-app-ec2-sg) <br />
Configure Storage = Default <br />
"Launch Instance" <br />

### Creating RDS

<br />
*Before creating RDS, we create a "Subnet group" first* <br />
<br />
Search RDS on AWS console page <br />
Select "Subnet groups" on side bar <br />
Click "Create DB subnet group" <br />
<br />
**Subnet group details** <br />
Name = my-db-sbg <br />
Description = my-db-sbg <br />
VPC = isw-vpc <br />
<br />
*Add Subnet* <br />
<br />
*Because we need at least 2 Availability zones, we create a new subnet in a different Availability zone* <br />
After creating a new subnet we select both availability zone and subnet. <br />
"Create" <br />
<br />
**Create Database** <br />
Select Databases on side bar <br />
Click "Create Database" <br />
Choose a Database creation method = Standard create <br />
Engine Options = MySQL <br />
Choose Version = Latest (Recomended) <br />
Templates = Dev/Test <br />
Availability And Durability = Single-DB-instance <br />
DB instance identifier = my-app-db-rds <br />
Master Username = root <br />
Credentials management = Self-Managed <br />
Master password = Create password for admin Database <br />
Confirm Master password = reEnter the password <br />
Instance configuration = Burstable classer (db.t3.micro)<br />
Storage = Default <br />
<br />
*Conectivity*<br />
<br />
VPC = isw vpc <br />
DB subnet group = my-db-sbg <br />
Public access = no <br />
VPC Security group = Choose existing (my-app-rds-sg) <br />
Availability Zone = Choose the same as ec2 <br />
"Create Database" <br/>
<br />

### Creating ALB
<br />
Before creating an ALB, we create a "Target group" first<br />
<br />
Search "Target Group" on AWS console page <br />
Click "Create target group" <br />
Choose target type = Instances <br />
Target group name = My-app-ec2-tg-80 <br />
Protocol:Port = HTTP(80) <br />
IP address type = IPv4 <br />
VPC = isw-vpc <br />
Health Checks point = Overide (80) <br />
"Next" <br />
Regiter Target = Select Ec2 app <br />
"Include As pending below"<br />
"Create" <br />
<br />
Repeat the steps for backend port 8080 <br />
<br />
*then we create a new public subnet because we need 2 Availability zones to create a loadbalancer*<br />
<br />
**Create Loadbalancer**<br />
<br />
Select "Load balancer" on sidebar Ec2 Service <br />
Click "Create load balancer" <br />
Load balancer type = Application load balancer <br />
<br />
*Network mappings*<br />
<br />
VPC = isw-vpc <br />
Security Group = my-app-alb-sg <br />
Listening and routing : <br/>
Protocol = HTTP <br />
Port = 80 <br />
Target group = my-app-ec2-tg-80 <br />
{Add Listener} <br />
Protocol = HTTPS <br />
Port = 443 <br />
Target group = my-app-ec2-tg-80 <br />
Default SSL/TLS Server Certificate : <br />
Certificate Source = from ACM <br />
Certificate = The certificate we create before <br />
Review And "Create Load Balancer" <br />
<br />
**Edit Rule ALB** <br />
<br />
Click "my-app-lb" <br />
Select "HTTP:80" <br />
"Manage Rules" -> "Edit Rules" <br />
Select listener Rules <br />
"Actions" -> "Edit Rules" <br />
Routing Actions = Redirect to URL <br />
Protocol = HTTPS(443) <br />
"Save Changes"<br />
<br />
*Add rule to API*<br />
<br />
Select "HTTPS:443" <br />
"Manage Rules" -> "Edit rule" <br />
Click "Add Rule" <br />
Name = (optional) -> "Next" <br />
Add condition = Host header -> api.domain.net (Confirm) <br />
"Next" <br />
Routing Actions = Forward to target group  -> "Next" <br />
Rule = Priority (1) <br />
"Create" <br />
<br />
**Create Recored DNS name ALB to Route53** <br/>
<br />
Search "Route53" on AWS console page <br />
Select "Hosted Zones" <br />
*Click on your Domain* <br/>
Click "Create Record" <br />
Record name = www <br />
Record type = A <br />
Turn on Alias <br />
Rute traffic to = Alias to ALB <br />
Region = Adjust to ALB <br />
Select the ALB that we created previously <br />
"Create Records" <br />
<br />
*Repeat these steps to type the record name api.domain.net*<br />
<br />

## 5. Creating a Simple Frontend (ReactJS + Nginx)
![6](https://github.com/user-attachments/assets/f36f7dcd-3aa1-438e-b6da-659d1d6a7fbf)
### React Setup <br />
- install node.js and npx <br /> 
- create a new react project <br />

<br />
```npx create-react-app``` 
<br />
- change the script in the App.js file in the src folder <br />
- Build apps <br />
<br />
 ```npm run build```
 <br />
- Test App <br />
<br />
```server -s build```
<br />
<br />
### Setup Nginx 
<br />
- Log in to the ec2-app server and install nginx <br />
<br />

```
ssh -i keypair.pem user@<IpEc2app>

sudo apt update
sudo apt install nginx

```
<br />
*** Make sure your connection is connected to the OpenVpn IP that we created *** <br />

- check nginx status <br />
<br />

```sudo systemctl status nginx```
<br />
### Test Nginx and copy the HTML that we built previously <br />
<br />
- Test Nginx <br />

Open the domain that we have created from Load Balancer or Route53 <br />
<br />
- Change the Permissions of the nginx html folder <br />
<br />
```sudo chown -R user:user html/```
<br />
***Make sure you are in the nginx folder***<br />
<br />
- Copy Projects <br />
<br />
```scp -i keypair.pem -r build/* user@<IpEc2aapp>:/usr/share/nginx/html/```
<br />
*Check again whether our app is running properly*<br />
<br />
## 6. Creating a Simple Backend (Golang + Systemd + MySQL)
<br />
![7](https://github.com/user-attachments/assets/1964b165-ef8f-4c56-8325-ae6c4d2b38bc)
<br />
### DB Setup <br />
<br />
- Go to the DB server and Login to MySQL<br />
<br />
```
ssh -i keypair.pem user@<IpDb> 
mysql -u user -p password
```
<br />
***Make sure you are connected to OpenVPN*** <br />
<br />
- Create data bases and tables <br />
<br />
```
create database appDB;
use appDB;
create table images(
         id int auto_increment primarykey,
         url varchar(255),
         created_at timestamp,
);
```
<br />
<br />
### Golang setup <br />
<br />
- Install Golang <br />
<br />
```https://go.dev/doc/install```
<br />
- Develop miniApi which is connected to DB <br />

- Test run <br />
<br />
```go run main.go```
<br />
- Test builds <br />
<br />
```go build main.go``` 
<br />
### Deploy and Setup Systemd
<br />
```sudo nano /etc/systemd/system/backend.service``` 
- Replace <br />
```
[unit]
Description=Go backend
After=multi-user.target

[service]
Type=simple
User=root
Group=root
ExecStart=/home/user/backend/main
Restart=always
RestartSec=3

[Install]
WantedBy=multi-usert.target
```
<br />
- Enable & Start Systemd service <br />
```
sudo systemctl enable backend.service
sudo systemctl start backend.service
```
<br />
- Test using a domain registered with Route53 <br />
    (api.domain.com) <br />
- if it works, change the apiURL in the Frontend project <br />
- don't forget to build and deploy the frontend to the Ec2 App <br />
<br />
## 7. Cloudfront Integration with ALB
![8](https://github.com/user-attachments/assets/f70e8bdb-2b98-4eb0-8b91-2a8e63414a2b)
<br />
### Create a certificate globally<br />
- Search for ACM in the AWS console search bar<br />
- Make sure you are in the US (Virginia) zone<br />
- Request a new certificate<br />
- Fill in the same domain and follow the creation instructions<br />
- Click ID certificate -> Create records in Route53<br />
- Wait until the Certificate status is "Issued"<br />
<br />
### Change HTTPS redirect rule to Cloudfront <br />
- Set loadbalancer we created <br />
- Select Http:80 -> Manage listeners -> add listeners <br />
- Change "Routing actions" -> "Forward to target groups" <br />
- Select target groups 80 <br />
- "Save" <br />
<br />
### Setup Cloudfront <br />
- Search for Cloudfront Service in AWS Console <br />
- Create a Cloudfront distribution <br />
<br />
***ORIGIN*** <br />
Origin domain = "Copy URL from Loadbalaner" <br />
protocol = httponly (80) <br />
Origin path = - <br />
Name = "Same as domain" <br />
Enable Origin = No <br />
***Default Cache behavior*** <br />
Viewers Protocol Policy = Redirect Http to https <br />
Cache Policy =- Caching Optimized <br />
Web app Firewall = Do not enable Security Protection (Temporary) <br />
***Settings*** <br />
Alternate Domain name = www.domain.com <br />
SSL = "use the certificate we created" <br />
"Create Distribution" <br />
<br />
### Point DNS to Cloudfront <br />
- Search for Route53 Service in AWS Console <br />
- Select the domain <br />
- Edit domain record (www.domain.com) <br />
- Route traffic to -> Alias ​​to Cloudfront distribution (select the id) <br />
- "Save" <br />
<br />

## 8. Review dan Test Aplikasi <br />
- Try accessing the Database without using OpenVpn to prevent leaks <br />
- Try adding some data to the Database <br />
- Try whether it appears in our app <br />
<br />
## 9. CI/CD using Jenkins
![9](https://github.com/user-attachments/assets/ec18b291-ca44-49e4-aec0-ed844d2ad1cc)
<br />
### Create a new instance to install Jenkins with type (t3.medium) <br />
Subnet = private-c/3 <br /> 
Firewall = Create new Security Group <br />
 - SSH (22) <br />
   Source type = Custom (OpenVpn) <br />
 - HTTP(80) <br />
   Source type = Custom (OpenVpn) <br />
 - HTTP(80) <br />
   Source type = Custom (Loadbalancer) <br />
"Launch Instance" <br />
<br />
### Access Instance using SSH <br />
```ssh -i key.pem user@<ipaddress>```
***make sure connected to OpenVpn*** <br />
- Install Depedencies <br />
1. Jenkins <br />
```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
2. Nodejs <br />
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
nvm install 22
node -v 
npm -v 
```
3. git <br />
```apt-get install git```
4 .go <br />
<br />
### Setup And Login to Jenkins <br />
1. Install plug-ins <br />
- Manage Jenkins -> Plug-ins -> Available plug-ins <br />
- SSH agents <br />
2. Add Credentials <br />
- Profile -> Credentials <br />
- Stores from parents -> click (global) -> Add credentials <br />
  Kind = SSH username with key <br />
  ID = "Create your ID" <br />
  Username = User AWS Ec2 <br />
  Private Key = (Enter directly) <br />
  "ADD" <br />
- Create <br />
<br />
### Create a Pipeline script <br />
Add new item <br />
Name = Deploy Backend <br />
Select "Pipeline" <br />
"OK" <br />
- Create a Script in the available pipeline column <br />
```
  stage('Build') {
     steps {
         sh 'GOOS=linux GOARCH=amd64 go build -o main main.go'
           }
   }
   stage('Remove existing main') {
      steps {
         script {
            sshagent(credentials: ["ssh-key"]) {
               sh "ssh ec2-user@${EC2_INSTANCE_IP} 'rm -rf ${APP_DIR}*'"
                   }
                 }
               }
    }
    stage('Deploy to EC2') {
       steps {
          script {
             sshagent(credentials: ["ssh-key"]) {
                sh "scp -o StrictHostKeyChecking=no main ec2-user@${EC2_INSTANCE_IP}:${APP_DIR}"
                    }
                  }
              }
     }
     stage('Restart service') {
        steps {
           script {
              sshagent(credentials: ["ssh-key"]) {
                 sh "ssh ec2-user@${EC2_INSTANCE_IP} 'sudo systemctl restart my_backend.service'"
                      }
                   }
      }

```
"Save" <br />
<br />
- Add Rules To Ec2 Security Groups From Jenkins Source <br />
  Type = SSH (22) <br />
  Source = Custom (Jenkins) <br />
- Add Cornjob to pollscm <br />
- Try running "Build Now" <br />
<br />
## 10. Cloudfront Cache Invalidation
![10](https://github.com/user-attachments/assets/db244773-e760-416a-abaa-a1514fe0e16a)
<br />
### Create an S3 Bucket <br />
- Search for S3 in the AWS console searchbar <br />
- Click "Create Bucket" <br />
   Bucket name = "give a name" <br />
   Object Ownership = All disabled <br />
   inactive "Block all public access" <br />
   Turn on "Acknowledge" <br />
   Bucket Versioning = Disable <br />
 "Create Buckets" <br />
- Try uploading sample data <br />
### Create a cloudfront distribution to point to sample data <br />
- Select cloudfront <br /> 
- Click "Create Distribution" <br />
    Origin Domain = use S3 Bucket url <br />
    Origin access = Origin access control setting <br />
    (so that it can only be accessed via cloudfront) <br />
    Origin access control = select S3 bucket url <br />
    Viewer Protocol Policy = Redirect Http to Https <br />
    Web app Firewall = Do not enable <br />
    Alternate domain name = image.domain.com <br />
    Custom SSL Certificate = Select the certificate that has been created <br />
  "Create Distribution"<br />
  <br />
  
### Set Permission <br />
- Open the Distribution that has been created <br />
- Select the origins bar <br />
- Select the origin name then edit <br />
- Click copy policy <br />
- Enter to S3 Bucket <br />
- Select the Permission bar <br />
- Bucket policy -> Edit <br />
- Paste Cloudfront policy <br />
 "Save changes" <br />
- Try accessing using Cloudfront Url <br />
<br />
***So that our data files are updated, we create a Cloudfront invalidation*** <br />
<br />
- To Cloudfront Service pages <br />
- Select Invalidations in a bar <br />
- Create Invalidations <br />
- Add object path = /* <br />
  "Create Invalidations" <br />
  
