

 

# Assessable_Activity2_WAD_2024_25



## 1.- EC2
### 1.1.- Create an EC2 instance
### 1.1.1. - Create a security group
At first step, I choose the “Security Groups” option in “Network and security” tag
 
Then, I click on create security group 

And I configure the security group with the name, 
description and inbound rules to allow access via ssh, 
http and https and I click on “add rule” for each one. 
At the last step, I click on create security group to 
finish the security group creation.
 
### 1.1.2.- Create an EC2 instance
First, I search Instances on the left menu and click on “launch instance” 
In this page, I configure the name of the instance, then I select Ubuntu-server 24.04 LTS Ami (the latest version).
 
A bit further down, I can choose the architecture, 64 bits arm in my case and te type of instance, “t2.micro” I think is enough to do this practice (and it is the cheapest).

Then, I choose a key pair name, and the security group created in the previous step will be applied in firewall configuration.
 
At the last, I click on launch instance and check the success message.
 
 
To assign an elastic-IP, first I need to search “elastic IP directions” inside of the “Network and security” section in the left menu
 
When I find it and click on it, I can start the setup.
First, I choose instance option and then, I choose the instance and, I think I had a mistake because, maybe, I need to select Allow reselecting this elastic IP, but I don’t sure. And, finally, I click on associate button to finish the elastic Ip association.
 
### 1.2 Install the necessary packages on the EC2 instance.
First, I need to connect with ssh. I can use the next command on PowerShell (I do the practice on windows):
** I need to note that I had issues because the file permissions were too open, and SSH rejected the connection. I need to change the permissions to read-only. I have this problem only one time and I don’t know why. [Resolved on 04-02 TC]**
-ssh -i <keysfile.pem> ubuntu@<elastic ip>
In the next steps I won’t attach many screenshots because all the steps involve Linux commands; it’s easier for me to just write them.
 
At first step, I do a system update
-sudo apt update && sudo apt upgrade -y
When the update is finished, I Install Apache with the next command (I use the -y flag to agree the middle-process confirmation.
-sudo apt install apache2 -y
Then, I need to install Node.js and npm.
-curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash – 
-sudo apt install nodejs -y
Then, I need to install PM2 to manage Node.js processes on the server.
-sudo npm install pm2 -g
When I install all the dependencies, I check the versions to make sure the installations were successful with “node -v” & “npm -v”
Then, I clone the GitHub repository and navigate into it with these two commands:
-git clone https://github.com/gisgarme/adivina-el-numero.git 
-cd adivina-el-numero
Then, I need to install the dependencies in the project with “npm install”.
Then, I need to navigate to “/etc/apache2/sites-available/”, make a copy of 000-default.config with the name assessable.config using cp command and then modify the “VirtualHost” to assign the public key in the ServerName and configure the “ProxyPass” and “ReverseProxyPass” to point to Vite server’s port. 
 
Then, I need to disable the 000-default.config with a2dissite and enable the assessable.config with a2ensite command. And then, to put on the new configuration, I need to restart apache service with “systemctl restart apache2” command.
And to verify that the installation was successful, I run this command: 
-pm2 start npm --name "vite-server" -- run dev -- --host and check in the browser to see if the application has been deployed. 


## 3.- S3 
### 3.1.- Create a bucket:
First, after login to the AWS account, I need to search for S3 service in the left menu.
Then, when I navigate to the S3 service, I click on “Create bucket” button. Before that, I need to select the correct region where I would create the bucket. However, when I try to set this region, I need to change it to the default one (US East - North Virginia) because it's the only way to have S3 permissions.
Then, the configuration page will be open and, inside of this page, I can setup the bucket configuration. In this practice, I only enter the name of the bucket, and the other configurations will be set to their default values. The name must be unique worldwide. Then, I click on “create bucket” at the bottom of the page. 
To navigate to the bucket configuration page, I need to click on the bucket name.
 
### 3.2.- Configure the bucket for static website hosting.
First, I go to the “Properties tab” of the created bucket and I click on it.
“In the Static website hosting” section, I click on it and enable the option and I configure the “Index document” parameter to “index.html” and I save the changes.

### 3.3.- Modifying the Bucket’s permissions.
First, I go to the “Permissions” tab and I search for “Block public access” section. Then, I can edit the configuration to disable the “Block public access” option. To confirm the new configuration, I need to type “confirm” and click on “confirm” button. 
Then, I will add a “bucket policy” using the policy generator. For that, I click on “Edit” within the “Bucket policy” section.
Within the “policy generator” I need to set the configuration as shown in the images below. 
When I type the bucket’s ARN, I need to type /* at the end. Then, I click on “Add Statement” to add the statement to generate the policy after. I click on the “Generate policy” button, copy and paste the policy inside of bucket policy section and I save the changes. 
with these steps, I finish the configuration to upload a static web site. 

### 3.4.- Upload the Application Files
First, I go to the “Objects” tab in the bucket.
However, to do that, first, I need to download the files of 
	https://github.com/gisgarme/adivina-el-numero-s3 repository.
	I can do that by cloning the repository or downloading a ZIP file and unzipping it. (Note: I can't drag the files because the files in this repository are the result of a build with Vite. If that weren’t the case, I would need to build the project to be able to host it). 
The last step is to verify that the page is working correctly by using the generated URL in the 'Static website hosting' section and opening it in a browser.
