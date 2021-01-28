# DevBops CICD Pipeline

## How to build CI/CD Pipeline for DevBops Microserices

![Jenkins_Pipeline_Diagram](https://github.com/anishmoktan/DevBops_CICD_Pipeline/blob/main/images/DevBops_Jenkins_Chart.jpg)

## Step 1: Spin up an EC2 on the Public Subnet   
- DevBops is currently live on our production VPC. For our CI/CD pipieline, we are going to create an EC2 in the production server's public subnet to run a Jenkins server. Altough it is not a best practice to test the pipeline in a production environment, doing this exercise will allow us to better see how other AWS components are connected to our overall infrastructure. Jenkins server is built in our public subnet to give the EC2 access to the internet, where we can later access to the user interface on **port 8080** and configure a pipeline. 


### How to Spin up a Public EC2
- On the **EC2 Dashboard**, click on ***Launch Instance***

1. Choose *Free Tier* Amazon Linux 2 AMI 64-bit(x86) 

2. Click on t2.micro *Free Tier* instance type

3. Leave most of the configurations the way it is besides these below:
    - Network: ***DevBops_Production_VPC***
    - Subnet: ***DevBops-Prod-Public-1***
    - Auto-assign Public IP: ***Enable***
        - We will need a public IP address for Jenkins and to ssh
    - *We are not going to add user data so leave it black and click on **Add Storage**

4. The root volume will be 8GiB so do not make any changes, click next on **Add Tag**

5. Since we are all sharing this AWS account, tag this EC2 with your name as below
    - Key: ***Name***
    - Value: ***YourName_Jenkins_Public***

6. Let's create a new security group specific to your settings for practice
    - Security group name: ***YourName_Jenkins_SG***
    - Description: ***YourName_Jenkins_SG***

        - ***You will need two inbound rules***
        - **Type** SSH **Protocol** TCP **Port Range** 22 **Source** My IP **Description** SSH from my computer
            > This rule allows us to ssh into the EC2's terminal
        - **Type** Custom TCP Rule **Protocol** TCP **Port Range** 8080 **Source** Custom [0.0.0.0/0,::/0] **Description** Jenkins UI on Browser
            > This rule allows anyone on the internet to access Jenkins UI on port 8080 of our IP address
        - Finally, click on **Review and Launch**
7. After reviewing the instance launch, choose the ***devbops_masterkey*** key pair 
    - [x] I acknowledge that I have access to the private key file (devbops_masterkey.pem), and that without this file, I won't be able to log into my instance
    - Clock on the box and then launch the instance


