# Hosting a Node.js Website on AWS

This guide explains how to host a Node.js application on AWS using an EC2 instance.

---

## Prerequisites
- An AWS account (sign up [here](https://aws.amazon.com/)).
- Basic knowledge of Node.js and Linux.
- Node.js application ready to deploy.

---

## Step 1: Create an AWS Account
1. Visit [AWS](https://aws.amazon.com/) and create a new account if you don’t already have one.
2. Complete the signup process, including billing information. (Make sure E-commerse/Online transaction is enabled in your debit/credit card).

---

## Step 2: Launch an EC2 Instance
1. Go to the **AWS Management Console**
2. Navigate to **EC2**. Or you can search **EC2** in the search option.
2. Click **Launch Instances**.
4. Enter Intance name. (Anything)
3. Choose an Application and OS Images (Amazon Machine Image):
   - Select **Ubuntu** or any other preferred AMI (`free tier eligible`) or higher, based on your needs.
4. Select an Instance Type:
   - Choose a **t2.micro** (`free tier eligible`) or higher, based on your needs.
5. Configure key pair:
   - Select key pair name if you have already created one.
   - If not click on `Create new key pair`.
        - Enter key pair name (Anything).
        - Key pair type: *Select RSA*.
        - Private key file format: *Select .pem*.
        - Click on `Create key pair` button and save the *.pem* file.
7. Configure Security Group(Network Settings):
   - Add the following rules:
     - **SSH**: Port 22, Source: Your IP
     - **HTTP**: Port 80, Source: Anywhere (0.0.0.0/0)
     - **HTTPS**: Port 443, Source: Anywhere (0.0.0.0/0)
6. Configure Storage:
   - Set the root volume size (e.g., 8 GB is usually sufficient).
8. Review and Launch:
   - Click **Review and Launch**, then **Launch**.

---

## Step 3: Purchase a Domain
A domain is required to provide a user-friendly URL for your website. There are multiple options for purchasing a domain:

### Option 1: Using Hostinger
1. Visit [Hostinger](https://www.hostinger.com/).
2. Search for your preferred domain name.
3. Add the domain to your cart and proceed to checkout.
4. Complete the payment process and register the domain.
5. Access your domain settings to manage DNS configurations.

### Option 2: Using Other Domain Registrars
If you prefer another provider, here are some alternatives:
- **Namecheap** ([namecheap.com](https://www.namecheap.com/))
- **GoDaddy** ([godaddy.com](https://www.godaddy.com/))
- **Google Domains** ([domains.google.com](https://domains.google.com/))
- **Bluehost** ([bluehost.com](https://www.bluehost.com/))

---

## Step 4: Configure Route 53 for DNS Management

1. Go to the **Route 53** dashboard in AWS.
2. Click **Create Hosted Zone**.
3. Enter your domain name (same as the one purchased from Hostinger or another registrar).
4. Select **Public Hosted Zone**.
5. Click **Create Hosted Zone**.

---  

## Step 5: Create DNS Records in Route 53  

1. In the **Route 53** dashboard, go to your **Hosted Zone**.  
2. Click **Create Record**.  
3. Configure the first record:  
   - **Record Name**: (Leave blank to map to the root domain, e.g., `example.com`).  
   - **Record Type**: `A - IPv4 address`.  
   - **Value**: Enter the **Public IPv4 address** of your EC2 instance.  
   - Click **Create Record**.  
4. Configure the second record:  
   - **Record Name**: `www` (to map `www.example.com` to your EC2 instance).  
   - **Record Type**: `A - IPv4 address`.  
   - **Value**: Enter the **Public IPv4 address** of your EC2 instance.  
   - Click **Create Record**.  

This step ensures that your domain correctly points to your EC2 instance.  

---  

## Step 6: Configure Name Servers in Hostinger  

1. In the **Route 53** dashboard, go to your **Hosted Zone**.  
2. Locate the **NS (Name Server) Records**.  
3. Copy the four Name Server (NS) values provided.  
4. Log in to your **Hostinger** account.  
5. Navigate to **DNS Settings** for your domain.  
6. Find the **Nameserver** section.  
7. Replace the existing nameservers with the four NS records copied from Route 53.  
8. Save the changes.  

It may take some time for DNS propagation to complete. Once done, your domain will be managed by AWS Route 53.  

---  

## Step 7: Configure Security Group for HTTP and HTTPS  

1. Open your **EC2 Instance** from the AWS Management Console.  
2. Scroll down and go to the **Security** tab.  
3. Click on the **Security Group** number.  
4. Click **Edit Inbound Rules**.  
5. Add the following rules (if not already present):  
   - **HTTP (Port 80, IPv4)**  
   - **HTTPS (Port 443, IPv4)**  
6. Click **Save Rules**.  

This ensures that your EC2 instance allows web traffic over HTTP and HTTPS.  

---

## Step 8: Connect to Instance

1. Go to your **EC2 Instance** in the AWS Management Console.
2. Click the **Connect** button.
3. Select the **SSH Client** tab.
4. Copy the SSH command example provided at the bottom (it should look like: `ssh -i "your-key.pem" ubuntu@ec2-xx-xx-xx-xx.region.compute.amazonaws.com`).
5. Open Command Prompt (CMD) in the directory where your `.pem` file is saved.
6. Paste the copied SSH command and press Enter.
7. Type "yes" if prompted about fingerprint authenticity.

If successful, you should see a welcome message and the Ubuntu command prompt.

---

## Step 9: Setting up Basic Firewall

Ubuntu servers can use the UFW (Uncomplicated Firewall) to manage incoming connections. Follow these steps to configure a basic firewall:

1. Check available UFW application profiles:
   ```bash
   sudo ufw app list
   ```

2. Allow SSH connections through the firewall:
   ```bash
   sudo ufw allow OpenSSH
   ```

3. Enable the firewall:
   ```bash
   sudo ufw enable
   ```
   Type 'y' when prompted to proceed

4. Verify the firewall status and rules:
   ```bash
   sudo ufw status
   ```
   You should see this output:
   ```bash
   Status: active

   To                         Action      From
   --                         ------      ----
   OpenSSH                    ALLOW       Anywhere
   OpenSSH (v6)              ALLOW       Anywhere (v6)
   ```

---

## Step 10: Installing Nginx

1. Update the local package index:
   ```bash
   sudo apt update
   ```

2. Install Nginx:
   ```bash
   sudo apt install nginx
   ```

---

## Step 11: Configuring the Firewall

1. List the available firewall application profiles:
   ```bash
   sudo ufw app list
   ```

2. Examine the Nginx profiles available:
   ```text
   Available applications:
     Nginx Full
     Nginx HTTP
     Nginx HTTPS
     OpenSSH
   ```

3. Enable HTTP traffic (port 80):
   ```bash
   sudo ufw allow 'Nginx HTTP'
   ```

4. Verify the firewall configuration:
   ```bash
   sudo ufw status
   ```

5. Confirm the status output:
   ```text
   Status: active

   To                         Action      From
   --                         ------      ----
   OpenSSH                    ALLOW       Anywhere                  
   Nginx HTTP                 ALLOW       Anywhere                  
   OpenSSH (v6)              ALLOW       Anywhere (v6)             
   Nginx HTTP (v6)           ALLOW       Anywhere (v6)
   ```

---

## Step 12: Checking your Web Server

1. Test Nginx by accessing your server's IP address in a web browser:
   ```
   http://your_server_ip
   ```
   You should see the default Nginx landing page

---

## Step 13: Setting Up Server Blocks

**Note:** Replace `your_domain` with your actual domain name in all commands and file contents.

1. Create a directory for your domain:
   ```bash
   sudo mkdir -p /var/www/your_domain/html
   ```

2. Assign directory ownership:
   ```bash
   sudo chown -R $USER:$USER /var/www/your_domain/html
   ```

3. Set appropriate permissions:
   ```bash
   sudo chmod -R 755 /var/www/your_domain
   ```

4. Navigate to the site directory:
   ```bash
   cd /var/www/your_domain/html/
   ```

5. Create and edit the index.html file:
   ```bash
   vim index.html
   ```

6. Add the following HTML content (press `i` to enter insert mode):
   ```html
   <html>
       <head>
           <title>Welcome to your_domain!</title>
       </head>
       <body>
           <h1>Success! The your_domain server block is working!</h1>
       </body>
   </html>
   ```

7. Save and exit (press `ESC`, then type `:wq`)

8. Navigate to the Nginx sites-available directory:
   ```bash
   cd /etc/nginx/sites-available/
   ```

**Note:** Replace `your_domain` with your actual domain name in the configuration file (3 places: root path and server_name lines).

9. Create and edit the server block configuration:
   ```bash
   sudo vim your_domain
   ```

10. Add the following configuration (press `i` to enter insert mode):
   ```nginx
   server {
           listen 80;
           listen [::]:80;

           root /var/www/your_domain/html;
           index index.html index.htm index.nginx-debian.html;

           server_name your_domain www.your_domain;

           location / {
                   try_files $uri $uri/ =404;
           }
   }
   ```

11. Save and exit (press `ESC`, then type `:wq`)

12. Create symbolic link to enable the site:
   ```bash
   sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
   ```

13. Verify the configuration files:
   ```bash
   cd ..
   cd sites-available
   ls
   ```

14. Confirm the available configuration files:
   ```text
   default  your_domain
   ```

15. Test Nginx configuration for syntax errors:
   ```bash
   sudo nginx -t
   ```

16. Restart Nginx to apply changes:
   ```bash
   sudo systemctl restart nginx
   ```

17. Test your configuration by visiting:
   `http://your_domain`

You should see the welcome page we created earlier with the success message.

---

## Step 14: Installing SSL Certificate Manager

1. Install Certbot and its Nginx plugin:
   ```bash
   sudo apt install certbot python3-certbot-nginx
   ```

---

## Step 15: Configuring Firewall for HTTPS

1. Check current firewall status:
   ```bash
   sudo ufw status
   ```

2. View current configuration:
   ```text
   Status: active

   To                         Action      From
   --                         ------      ----
   OpenSSH                    ALLOW       Anywhere                  
   Nginx HTTP                 ALLOW       Anywhere                  
   OpenSSH (v6)              ALLOW       Anywhere (v6)             
   Nginx HTTP (v6)           ALLOW       Anywhere (v6)
   ```

3. Update firewall rules for HTTPS:
   ```bash
   sudo ufw allow 'Nginx Full'
   sudo ufw delete allow 'Nginx HTTP'
   ```

4. Verify updated configuration:
   ```bash
   sudo ufw status
   ```

5. Confirm new settings:
   ```text
   Status: active

   To                         Action      From
   --                         ------      ----
   OpenSSH                    ALLOW       Anywhere
   Nginx Full                 ALLOW       Anywhere
   OpenSSH (v6)              ALLOW       Anywhere (v6)
   Nginx Full (v6)           ALLOW       Anywhere (v6)
   ```

---

## Step 16: Obtaining SSL Certificate

1. Run Certbot with Nginx plugin:
   ```bash
   sudo certbot --nginx -d your_domain -d www.your_domain
   ```

2. Complete the certificate process:
   - Enter your email address when prompted
   - Accept the terms of service
   - Choose whether to share your email with EFF
   - Choose whether to redirect HTTP traffic to HTTPS

Your site is now secured with SSL/TLS encryption and accessible via HTTPS.

You can reload the address and confirm it.

---

Here's Step 17 formatted to match your README.md style:

## Step 17: Setting Up SSL Auto-Renewal

1. Test the auto-renewal process:
   ```bash
   sudo certbot renew --dry-run
   ```

Your SSL certificate will now automatically renew before expiration. Let's Encrypt certificates are valid for 90 days, and the automated renewal process will attempt renewal when the certificate is 30 days from expiring.

---

Here's Step 18 formatted in the same style as your README.md:

## Step 18: Installing NodeJS on Ubuntu

1. Update package lists and upgrade existing packages:
   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

2. Install curl if not already present:
   ```bash
   sudo apt install curl -y
   ```

3. Install Node Version Manager (nvm):
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
   ```

4. Load nvm into current session:
   ```bash
   source ~/.bashrc
   ```

5. Install the latest LTS version of Node.js:
   ```bash
   nvm install --lts
   ```

6. Set default Node.js version:
   ```bash
   nvm alias default 18.18.2
   ```
   Note: Replace 18.18.2 with the LTS version installed in step 5

7. Verify installations:
   ```bash
   node -v
   npm -v
   ```

Your system now has Node.js and npm (Node Package Manager) installed and ready to use.

8. Install build-essential for npm package compilation:
   ```bash
   sudo apt install build-essential
   ```

This package is required for compiling and installing certain npm packages that need to build from source code.

---

## Step 20: Installing PM2 Process Manager

1. Install PM2 globally using npm:
   ```bash
   npm install -g pm2
   ```

2. Verify PM2 installation:
   ```bash
   pm2 --version
   ```

---

## Step 21: Configuring Nginx as a Reverse Proxy

1. Edit your Nginx site configuration:

   Change `example.com` with your domain name
   ```bash
   sudo vim /etc/nginx/sites-available/example.com
   ```

2. Inside the `server` block, locate the `location /` block. Comment out any existing content within it using `#`, then add the following configuration:
   ```nginx
   location / {
       proxy_pass http://localhost:3000;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
   }
   ```
   Note: Replace port 3000 if your application uses a different port

3. Test Nginx configuration for syntax errors:
   ```bash
   sudo nginx -t
   ```

4. If no errors are found, restart Nginx:
   ```bash
   sudo systemctl restart nginx
   ```

Your Node.js application should now be accessible through your domain, with Nginx acting as a reverse proxy.

---

## Step 22: Cloning the Git Repository

1. Navigate to your home directory:
   ```bash
   cd ~
   ```

2. Clone your repository:
   ```bash
   git clone <git repo link>
   ```

3. Verify the repository was cloned:
   ```bash
   ls
   ```

4. Navigate into the project directory:
   ```bash
   cd repository_name
   ```

5. View project contents:
   ```bash
   ls
   ```

Note: Replace `<repo link>` with your actual repository URL and `repository_name` with the name of your cloned repository directory.

---

## Step 23: Setting Up Project Dependencies and Environment

1. Install project dependencies:
   ```bash
   npm install
   ```

2. Create and edit environment file:
   ```bash
   vim .env
   ```

3. Add environment variables:
   - Press `i` to enter insert mode
   - Paste your environment variables
   ```env
   # Example format:
   DATABASE_URL=your_database_url
   API_KEY=your_api_key
   PORT=3000
   ```

4. Save and exit:
   - Press `ESC` to exit insert mode
   - Type `:wq` and press `Enter`

Your project now has all required dependencies installed and environment variables configured.

---

## Step 24: Viewing All Project Files Including Hidden Files

1. View all files, including hidden ones:
   ```bash
   ls -a
   ```
   Note: `-a` shows all files including those starting with "." (hidden files)

2. For more detailed information, use:
   ```bash
   ls -la
   ```
   This shows:
   - File permissions
   - Number of links
   - Owner
   - Group
   - File size
   - Last modified date
   - File/directory name

---

## Step 25: Start the Application with PM2

1. Configuring and Starting PM2
   ```bash
   pm2 start index.js --name "app_name"
   ```

   **Explanation:**
   - `pm2 start index.js` → Starts the app with PM2.
   - `--name "app_name"` → Assigns a custom name for easier management.

2. Check Running PM2 Processes
   ```bash
   pm2 list
   ```

3. To check real-time logs:
   ```bash
   pm2 logs app_name
   ```

4. Enable Auto-Restart on System Reboot
   ```bash
   pm2 startup
   ```
   This will generate a command. Run the suggested command.

   Then, save the process list:
   ```bash
   pm2 save
   ```

---

5. Manage Your Application
   | Command                     | Description                  |
   |-----------------------------|------------------------------|
   | `pm2 restart app_name`      | Restart the application     |
   | `pm2 stop app_name`         | Stop the application        |
   | `pm2 delete app_name`       | Remove the application      |
   | `pm2 logs app_name`         | View logs in real-time      |

---


## 🎯 Troubleshooting
   ```bash
   If you face any issues, try turning it off and on again. And if that doesn’t work, summon a software wizard (aka, Google it). 🔮😄

   Happy Coding! 🚀
   ```