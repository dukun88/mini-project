# Mini Project
## 1. Architecture
![1](https://github.com/user-attachments/assets/bef9c3aa-f615-4623-bd31-3037deb11c59)

## 2. Creating Domains on Route53 and ACM
![2](https://github.com/user-attachments/assets/24598ac5-29a0-40a0-8a71-e110305ad156)
### Register Domain on Route53
Search Route53 on AWS console page

Click “Regitered Domains” in the bar

“Regiter Domain” to create a new domain

Enter the domain name that will be used (make sure the domain is available)(“Proceed to Checkout”)  

Then determine how long the contract is according to the budget or rental year, also determine whether we want to automatically renew or not (“Next”) 

Then fill in the appropriate domain orderer's personal data (“Next”) 
Review and submit the domain order (“Submit”)  
### Request Certificate in ACM

Search ACM (AWS certificate manager) on AWS console pages

Click “Request” to request a new certificate 

Select “Public certificate” 

Then fill in the domain name to be registered, also register the sub-domain by adding (*.) to the domain prefix 

Use validation method (DNS / Email), and Select key algorithm (RSA, .etc) (“Request”)

Register CNAME for Validation by clicking “Create Records in Route53” 

Check DNS in Route53 whether the CNAME was successfully added

Check again whether the certificate is valid and applied to DNS 


## 3. Creating Security Groups for EC2, RDS, and ALB
### Architecture with OpenVPN and VPC Information
![3](https://github.com/user-attachments/assets/46c2b265-91bd-4c2f-bd13-a88547de9144)
#### Create a VPC and Subnet with a diagram as follows:

![Your paragraph text](https://github.com/user-attachments/assets/2487ba04-c423-42c7-872a-43d784b6a6c4)
***Create VPC*** 

Search VPC on the AWS console page 

Select "Create VPC" 

Resource to create : "VPC only" 

Enter a name VPC : "isw-vpc" 

Enter an IPv4 CIDR block : "10.1.0.0/16" 

"No IPv6 CIDR Block" In Ipv6 CIDR block 

Tenancy : "default" 

Select Create VPC 



***Create Subnets*** 



Select Subnets on Bar VPC Resource 

Select "Create Subnet" 

VPC ID : "Use the VPC ID that we created previously" 

*Subnet 1 of 3*


Subnet name : "isw-public-subnet-a"

Availability Zone : "select the one you want to use"

IPv4 VPC CIDR block : "10.1.0.0/16"

IPv4 subnet CIDR block : "10.1.1.0/26"


Add new subnet 


*Subnet 2 of 3*


Subnet name : "isw-private-subnet-app-a"

Availability Zone : "Select the same as public subnet"

IPv4 VPC CIDR block : "10.1.0.0/16"

IPv4 subnet CIDR block : "10.1.1.128/27"


Add new subnet 


*Subnet 3 of 3*


Subnet name : "isw-private-subnet-app-a"

Availability Zone : "Select the same as public subnet

IPv4 VPC CIDR block : "10.1.0.0/16"

IPv4 subnet CIDR block : "10.1.1.192/27"

"Create Subnet" 



*Connect the public subnet to the internet gateway using route tables* 

*Connect the private subnet to the NAT gateway using route tables*



#### Create OpenVPN for DevOps setup and access Private Resources


Search EC2 Instance on AWS console page  

Click “Launch Instances” to create new instance  

Name : "isw-openvpn" 

Select "Browse more AMIs"

Search "Openvpn" on search bar 

Select "OpenVPN Acces server" 

Click "Subcribe now" 

Instance type : t2.micro 

Key pair : "Create new key pair"



*Make sure you store the key pair safely*



Network settings:

 1. VPC : Use the VPC that we created previously 
 
 2. Subnet : Use the Public-subnet
 
 3. Auto-assign public IP : Enable 
 
 4. Security group name : Replace name with "my-openvpn-sg" 
 
 5. Description : default
 
 6. Inbound security group rules: default 
 
"Launh Instance" 





**Setup OpenVpn** 




Login to openvpn



```

$ssh -i keypair.pem root@ipOpenvpn

```



follow the setup instructions



*Try to log in to the admin page with the username and password that we specified during setup*



ipOpenvpn:943/admin (for Admin) 

ipOpenvpn:943 (for Client)


### Creating a Security Group
![4](https://github.com/user-attachments/assets/9a851805-a28f-4d59-b613-99a4a80fb548)


Search Security Group on AWS console page 

Click "Create Security group" to create new security group 



***Security Group LoadBalancer*** 



Name : my-app-alb-sg 

Description : my-app-alb-sg 

VPC : Use the VPC that we created previously 

*Inbound rules* 

Add rules according to the diagram : 



1.Type : HTTP, Port : 80, Source : Anywhere IPv4 (0.0.0.0/0) 

2.Type : HTTPS, Port : 443, Source : Anywhwre IPv4 (0.0.0.0/0) 



*Outbound rules* 

Default (0.0.0.0/0) 

Add tag Name : my-app-alb-sg 

"Create Security Group" 



***Security Group App Ec2*** 



Name : my-app-ec2-sg 

Description : my-app-ec2-sg 

VPC : Use the VPC that we created previously 

*Inbound rules* 

Add rules according to the diagram : 



1.Type : SSH, Port : 22, Source : my-openvpn-sg 

2.Type : Custom TCP, Port : 3000, Source : my-openvpn-sg 

3.Type : Custom TCP, Port : 8080, Source : my-openvpn-sg 

4.Type : Custom TCP, Port : 8080, Source : my-app-alb-sg 

5.Type : HTTP, Port : 80, Source : my-app-alb-sg 

6.Type : HTTPS, Port : 443, Source : my-app-alb-sg 



*Outbound rules* 

Default (0.0.0.0/0) 

Add tag Name : my-app-ec2-sg 
 
"Create Security Group" 



***Security Group App Database*** 



Name : my-app-rds-sg 

Description : my-app-rds-sg 

VPC : Use the VPC that we created previously 

*Inbound rules* 

Add rules according to the diagram : 



1.Type : MYSQL, Port : 3306, Source : my-openvpn-sg 

2.Type : MYSQL, Port : 3306, Source : my-app-alb-sg 



*Outbound rules* 

Default (0.0.0.0/0) 

Add tag Name : my-app-rds-sg 
 
"Create Security Group" 





## 4. Create EC2, RDS, and ALB
![5](https://github.com/user-attachments/assets/8ff11ffc-3067-42f5-b1cf-5859ea2c77e8)


### Creating EC2
Search EC2 on AWS console page 

Click "Launch Instances" to create new EC2 instance 

Name and tag = my-app-ec2 

Applications and OS image (AMI) =  Amazon Linux 

Instance type = t3.micro 

Keypair = keypair that we have created 



*Network Settings* 

VPC = isw-vpc 

Subnet = isw-private-subnet-a 

Auto-assign public ip = Disable 

Firewall = Select existing security group (my-app-ec2-sg) 

Configure Storage = Default 

"Launch Instance" 


### Creating RDS



*Before creating RDS, we create a "Subnet group" first* 



Search RDS on AWS console page 

Select "Subnet groups" on side bar 

Click "Create DB subnet group" 



**Subnet group details** 

Name = my-db-sbg 

Description = my-db-sbg 

VPC = isw-vpc 



*Add Subnet* 



*Because we need at least 2 Availability zones, we create a new subnet in a different Availability zone* 

After creating a new subnet we select both availability zone and subnet. 

"Create" 



**Create Database** 

Select Databases on side bar 

Click "Create Database" 

Choose a Database creation method = Standard create 

Engine Options = MySQL 

Choose Version = Latest (Recomended) 

Templates = Dev/Test 

Availability And Durability = Single-DB-instance 

DB instance identifier = my-app-db-rds 

Master Username = root 

Credentials management = Self-Managed 

Master password = Create password for admin Database 

Confirm Master password = reEnter the password 

Instance configuration = Burstable classer (db.t3.micro)

Storage = Default 



*Conectivity*



VPC = isw vpc 

DB subnet group = my-db-sbg 

Public access = no 

VPC Security group = Choose existing (my-app-rds-sg) 

Availability Zone = Choose the same as ec2 

"Create Database" 


### Creating ALB


*Before creating an ALB, we create a "Target group" first*



Search "Target Group" on AWS console page 

Click "Create target group" 

Choose target type = Instances 

Target group name = My-app-ec2-tg-80 

Protocol:Port = HTTP(80) 

IP address type = IPv4 

VPC = isw-vpc 

Health Checks point = Overide (80) 

"Next" 

Regiter Target = Select Ec2 app 

"Include As pending below"

"Create" 



Repeat the steps for backend port 8080 



*then we create a new public subnet because we need 2 Availability zones to create a loadbalancer*



**Create Loadbalancer**



Select "Load balancer" on sidebar Ec2 Service 

Click "Create load balancer" 

Load balancer type = Application load balancer 



*Network mappings*



VPC = isw-vpc 

Security Group = my-app-alb-sg 

Listening and routing : 
Protocol = HTTP 

Port = 80 

Target group = my-app-ec2-tg-80 

{Add Listener} 

Protocol = HTTPS 

Port = 443 

Target group = my-app-ec2-tg-80 

Default SSL/TLS Server Certificate : 

Certificate Source = from ACM 

Certificate = The certificate we create before 

Review And "Create Load Balancer" 



**Edit Rule ALB** 



Click "my-app-lb" 

Select "HTTP:80" 

"Manage Rules" -> "Edit Rules" 

Select listener Rules 

"Actions" -> "Edit Rules" 

Routing Actions = Redirect to URL 

Protocol = HTTPS(443) 

"Save Changes"



*Add rule to API*



Select "HTTPS:443" 

"Manage Rules" -> "Edit rule" 

Click "Add Rule" 

Name = (optional) -> "Next" 

Add condition = Host header -> api.domain.net (Confirm) 

"Next" 

Routing Actions = Forward to target group  -> "Next" 

Rule = Priority (1) 

"Create" 



**Create Recored DNS name ALB to Route53** 


Search "Route53" on AWS console page 

Select "Hosted Zones" 

*Click on your Domain* 
Click "Create Record" 

Record name = www 

Record type = A 

Turn on Alias 

Rute traffic to = Alias to ALB 

Region = Adjust to ALB 

Select the ALB that we created previously 

"Create Records" 



*Repeat these steps to type the record name api.domain.net*




## 5. Creating a Simple Frontend (ReactJS + Nginx)
![6](https://github.com/user-attachments/assets/f36f7dcd-3aa1-438e-b6da-659d1d6a7fbf)
### React Setup 

- install node.js and npx 
 
- create a new react project 




```npx create-react-app``` 


- change the script in the App.js file in the src folder 

- Build apps 



 ```npm run build```
 
 
- Test App 



```server -s build```




### Setup Nginx 


- Log in to the ec2-app server and install nginx 




```
ssh -i keypair.pem user@<IpEc2app>

sudo apt update
sudo apt install nginx

```


*Make sure your connection is connected to the OpenVpn IP that we created* 


- check nginx status 




```sudo systemctl status nginx```


### Test Nginx and copy the HTML that we built previously 



- Test Nginx 


Open the domain that we have created from Load Balancer or Route53 



- Change the Permissions of the nginx html folder 



```sudo chown -R user:user html/```


*Make sure you are in the nginx folder*



- Copy Projects 



```scp -i keypair.pem -r build/* user@<IpEc2aapp>:/usr/share/nginx/html/```


*Check again whether our app is running properly*



## 6. Creating a Simple Backend (Golang + Systemd + MySQL)


![7](https://github.com/user-attachments/assets/1964b165-ef8f-4c56-8325-ae6c4d2b38bc)


### DB Setup 



- Go to the DB server and Login to MySQL



```
ssh -i keypair.pem user@<IpDb> 
mysql -u user -p password
```


***Make sure you are connected to OpenVPN*** 



- Create data bases and tables 



```
create database appDB;
use appDB;
create table images(
         id int auto_increment primarykey,
         url varchar(255),
         created_at timestamp,
);
```




### Golang setup 



- Install Golang 



```https://go.dev/doc/install```


- Develop miniApi which is connected to DB 


- Test run 



```go run main.go```


- Test builds 



```go build main.go``` 


### Deploy and Setup Systemd


```sudo nano /etc/systemd/system/backend.service``` 
- Replace 

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


- Enable & Start Systemd service 

```
sudo systemctl enable backend.service
sudo systemctl start backend.service
```


- Test using a domain registered with Route53 

    (api.domain.com) 
    
- if it works, change the apiURL in the Frontend project 

- don't forget to build and deploy the frontend to the Ec2 App 



## 7. Cloudfront Integration with ALB
![8](https://github.com/user-attachments/assets/f70e8bdb-2b98-4eb0-8b91-2a8e63414a2b)


### Create a certificate globally

- Search for ACM in the AWS console search bar

- Make sure you are in the US (Virginia) zone

- Request a new certificate

- Fill in the same domain and follow the creation instructions

- Click ID certificate -> Create records in Route53

- Wait until the Certificate status is "Issued"



### Change HTTPS redirect rule to Cloudfront 

- Set loadbalancer we created 

- Select Http:80 -> Manage listeners -> add listeners 

- Change "Routing actions" -> "Forward to target groups" 

- Select target groups 80 

- "Save" 



### Setup Cloudfront 

- Search for Cloudfront Service in AWS Console 

- Create a Cloudfront distribution 



***ORIGIN*** 

Origin domain = "Copy URL from Loadbalaner" 

protocol = httponly (80) 

Origin path = - 

Name = "Same as domain" 

Enable Origin = No 

***Default Cache behavior*** 

Viewers Protocol Policy = Redirect Http to https 

Cache Policy =- Caching Optimized 

Web app Firewall = Do not enable Security Protection (Temporary) 

***Settings*** 

Alternate Domain name = www.domain.com 

SSL = "use the certificate we created" 

"Create Distribution" 



### Point DNS to Cloudfront 

- Search for Route53 Service in AWS Console 

- Select the domain 

- Edit domain record (www.domain.com) 

- Route traffic to -> Alias ​​to Cloudfront distribution (select the id) 

- "Save" 




## 8. Review dan Test Aplikasi 

- Try accessing the Database without using OpenVpn to prevent leaks 

- Try adding some data to the Database 

- Try whether it appears in our app 



## 9. CI/CD using Jenkins
![9](https://github.com/user-attachments/assets/ec18b291-ca44-49e4-aec0-ed844d2ad1cc)


### Create a new instance to install Jenkins with type (t3.medium) 

Subnet = private-c/3 
 
Firewall = Create new Security Group 

 - SSH (22) 
 
   Source type = Custom (OpenVpn) 
   
 - HTTP(80) 
 
   Source type = Custom (OpenVpn) 
   
 - HTTP(80) 
 
   Source type = Custom (Loadbalancer) 
   
"Launch Instance" 



### Access Instance using SSH 

```ssh -i key.pem user@<ipaddress>```

***make sure connected to OpenVpn*** 

- Install Depedencies 

1. Jenkins 

```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
2. Nodejs 

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
nvm install 22
node -v 
npm -v 
```
3. git 

```apt-get install git```
4 .go 



### Setup And Login to Jenkins 

1. Install plug-ins 

- Manage Jenkins -> Plug-ins -> Available plug-ins 

- SSH agents 

2. Add Credentials 

- Profile -> Credentials 

- Stores from parents -> click (global) -> Add credentials 

  Kind = SSH username with key 
  
  ID = "Create your ID" 
  
  Username = User AWS Ec2 
  
  Private Key = (Enter directly) 
  
  "ADD" 
  
- Create 



### Create a Pipeline script 

Add new item 

Name = Deploy Backend 

Select "Pipeline" 

"OK" 

- Create a Script in the available pipeline column 

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
"Save" 



- Add Rules To Ec2 Security Groups From Jenkins Source 

  Type = SSH (22) 
  
  Source = Custom (Jenkins) 
  
- Add Cornjob to pollscm 

- Try running "Build Now" 



## 10. Cloudfront Cache Invalidation
![10](https://github.com/user-attachments/assets/db244773-e760-416a-abaa-a1514fe0e16a)


### Create an S3 Bucket 

- Search for S3 in the AWS console searchbar 

- Click "Create Bucket" 

   Bucket name = "give a name" 
   
   Object Ownership = All disabled 
   
   inactive "Block all public access" 
   
   Turn on "Acknowledge" 
   
   Bucket Versioning = Disable 
   
 "Create Buckets" 
 
- Try uploading sample data 

### Create a cloudfront distribution to point to sample data 

- Select cloudfront 
 
- Click "Create Distribution" 

    Origin Domain = use S3 Bucket url 
    
    Origin access = Origin access control setting 
    
    (so that it can only be accessed via cloudfront) 
    
    Origin access control = select S3 bucket url 
    
    Viewer Protocol Policy = Redirect Http to Https 
    
    Web app Firewall = Do not enable 
    
    Alternate domain name = image.domain.com 
    
    Custom SSL Certificate = Select the certificate that has been created 
    
  "Create Distribution"
  
  
  
  
### Set Permission 

- Open the Distribution that has been created 

- Select the origins bar 

- Select the origin name then edit 

- Click copy policy 

- Enter to S3 Bucket 

- Select the Permission bar 

- Bucket policy -> Edit 

- Paste Cloudfront policy 

 "Save changes" 
 
- Try accessing using Cloudfront Url 



***So that our data files are updated, we create a Cloudfront invalidation*** 



- To Cloudfront Service pages 

- Select Invalidations in a bar 

- Create Invalidations 

- Add object path = /* 

  "Create Invalidations" 
