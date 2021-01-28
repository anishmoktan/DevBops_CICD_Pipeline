# DevBops CICD Pipeline


## How to build CI/CD Pipeline for DevBops Microserices

![Jenkins_Pipeline_Diagram](https://github.com/anishmoktan/DevBops_CICD_Pipeline/blob/main/images/DevBops_Jenkins_Chart.jpg)

## Step 1: Spin up an EC2 on the Public Subnet   
- DevBops is currently live on our production VPC. For our CI/CD pipieline, we are going to create an EC2 in the production server's public subnet to run a Jenkins server. Altough it is not a best practice to test the pipeline in a production environment, doing this exercise will allow us to better see how other AWS components are connected to our overall infrastructure. Jenkins server is built in our public subnet to give the EC2 access to the internet, where we can later access to the user interface on **port 8080** and configure a pipeline. 


- 
- Network: 	***DevBops_Production_VPC***
- Subnet: ***DevBops-Prod-Public-1***
- Auto-assign Public IP: ***Enable***