# DevBops CICD Pipeline

## How to build CI/CD Pipeline for DevBops Microserices

![Jenkins_Pipeline_Diagram](https://github.com/anishmoktan/DevBops_CICD_Pipeline/blob/main/images/DevBops_Jenkins_Chart.jpg)

## Step 1: Spin up an EC2 on the Public Subnet   
DevBops is currently live on our production VPC. For our CI/CD pipeline, we are going to create an EC2 in the production server's public subnet to run a Jenkins server. Although it is not a best practice to test the pipeline in a production environment, doing this exercise will allow us to better see how other AWS components are connected to our overall infrastructure. Jenkins server is built in our public subnet to give the EC2 access to the internet, where we can later access to the user interface on **port 8080** and configure a pipeline. 

### How to Spin up a Public EC2
On the **EC2 Dashboard**, click on ***Launch Instance***

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
    - Security group name: ***YourName_Jenkins_SG_Public***
    - Description: ***YourName_Jenkins_SG***

        - ***You will need two inbound rules***
        - **Type:** SSH **Protocol:** TCP **Port Range:** 22 **Source:** My IP **Description:** SSH from my computer
            > This rule allows us to ssh into the EC2's terminal
        - **Type:** Custom TCP Rule **Protocol:** TCP **Port Range:** 8080 **Source:** Custom [0.0.0.0/0,::/0] **Description:** Jenkins UI on Browser
            > This rule allows anyone on the internet to access Jenkins UI on port 8080 of our IP address
        - Finally, click on **Review and Launch**
7. After reviewing the instance launch, choose the ***devbops_masterkey*** key pair 
- [x] I acknowledge that I have access to the private key file (devbops_masterkey.pem), and that without this file, I won't be able to log into my instance
- Click on the box and then launch the instance

## Step 2: Spin up an EC2 on the Private Subnet   
Bevbops' backend runs in the private subnet and we will practice running all three microservice containers in one EC2. Although it will cause a single point of failure, we are going to practice running multilpe containers in different ports in one EC2. 

### How to Spin up a Private EC2
On the **EC2 Dashboard**, click on ***Launch Instance***

1. Choose *Free Tier* Amazon Linux 2 AMI 64-bit(x86) 

2. Click on t2.micro *Free Tier* instance type

3. Again, leave most of the configurations the way it is besides these below:
    - Network: ***DevBops_Production_VPC***
    - Subnet: ***DevBops-Prod-Private-1***
    - Auto-assign Public IP: ***Use subnet setting (Disable)***
        - Our private EC2 will use the NAT gateway to access the internet so it will not need its unique public ip
    - *We are not going to add user data so leave it black and click on **Add Storage**

4. The root volume will be 8GiB so do not make any changes, click next on **Add Tag**

5. Since we are all sharing this AWS account, tag this EC2 with your name as below
    - Key: ***Name***
    - Value: ***YourName_Jenkins_Private***

6. Create a new security group for the private EC2
    - Security group name: ***YourName_Jenkins_SG_Private***
    - Description: ***YourName_Jenkins_SG_Private***

        - ***You will need three inbound rules***
        - **Type:** SSH **Protocol:** TCP **Port Range:** 22 **Source:** 172.25.22.75/32 **Description:** SSH from jumpbox
            > This rule allows us to first ssh into the jumpbox EC2 and then ssh into the private EC2
        - **Type:** SSH **Protocol:** TCP **Port Range:** 22 **Source:** YourName_Jenkins_SG **Description:** SSH from Jenkins server
            > Choose the source to the security group you just made for Jenkin's public EC2 so it has the SSH access
            > ***If this method does not work, add another SSH rule with private IP address of your public EC2 as source ***
        - Finally, click on **Review and Launch**
7. After reviewing the instance launch, once again choose the ***devbops_masterkey*** key pair 
- [x] I acknowledge that I have access to the private key file (devbops_masterkey.pem), and that without this file, I won't be able to log into my instance
- Click on the box and then launch the instance

## Step 3: SSH into Jenkins Server and Configure
We will now ssh into our Jenkins server and install all of the packages required to run Jenkins, use git and build Docker images 
- On your local terminal, cd into the directory that has the devbops masterkey
- Once you're there, ssh into your Jenkins server using this command:  
    > **ssh -i "devbops_masterkey.pem" ec2-user@{Jenkins'_Public_IP_Address}**
- If you're successful, follow these steps/tutorials for each installations:
    1. Install [Jenkins](https://www.youtube.com/watch?v=jmm8DsosBqw&ab_channel=AutomationStepbyStep-RaghavPal)
    2. Install [Docker](https://www.youtube.com/watch?v=d05z5ZO4vbE&t=182s&ab_channel=YBMadhuSudhanReddy)
    3. Install [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- Once Jenkins is set up and running, on your browser, access your the server in port 8080
> ex. {ip_address}:8080
> Make sure all of the recommended Jenkins plugins are installed 

## Step 4: Create a Pipeline on Jenkins
Sign in to your Jenkins account and create a new pipeline by clicking on **New Item** on the left tab
Let's begin with our user microservice

1. Enter ***DevBops_User_Pipeline** for item name and then click on **Pipeline**
2. Let's leave all of the boxes in General tab blank and move on to Build Triggers
    - Click on GitHub hook for enabling webhook 
    - [x] GitHub hook trigger for GITScm polling
3. On your GitHub repo, go to the settings and add a Webhook
    - Payload URL will be: **http://{Jenkins_Ip}:8080/github-webhook/**
    - Content type: **application/json**
    - Secret: Leave it blank
    - Which events would you like to trigger this webhook?: **Send me everything**
    - [x] Active
    - Add Webhook
4. 


