
# EC2 Setup & Application Deployment (Summary)
## 1. Launch an EC2 Instance

- Create a Security Group:
    - Go to "Security Groups" under "Network and Security" and create a new one.
    - Set name, description, and inbound rules for SSH, HTTP, and HTTPS.

- Launch an EC2 Instance:
    - Select Ubuntu Server 24.04 LTS, 64-bit ARM, and t2.micro.
    - Choose a key pair and apply the Security Group.
    - Click "Launch Instance" and verify success.

- Assign an Elastic IP:
    - Go to "Elastic IP addresses" under "Network and Security".
    - Associate the IP with the instance.

## 2. Install Required Packages

- Connect via SSH:
    - ssh -i <keysfile.pem> ubuntu@<elastic-ip> 
    - (Fix permission issue if needed)

- Update system & install dependencies:
    - sudo apt update && sudo apt upgrade -y  
    - sudo apt install apache2 -y  
    - curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -  
    - sudo apt install nodejs -y  
    - sudo npm install pm2 -g  
    - (Verify with node -v & npm -v)

- Deploy the application:
    - git clone https://github.com/gisgarme/adivina-el-numero.git  
    - cd adivina-el-numero  
    - npm install  

## 3. Configure Apache Proxy

- Copy & modify Apache config:

    - cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/assessable.conf  
    - (Edit ServerName, ProxyPass, and ReverseProxyPass in the new config file)

- Enable site & restart Apache:
   - sudo a2dissite 000-default.conf  
   - sudo a2ensite assessable.conf  
   - sudo systemctl restart apache2  

- Start the Vite server:
   - pm2 start npm --name "vite-server" -- run dev -- --host  
   - (Check the app in the browser to confirm deployment.)
