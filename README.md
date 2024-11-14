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

Search ACM (AWS certificate manager) on AWS console page
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
