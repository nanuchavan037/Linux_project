# Linux_project

# SMTP Configuration for Localhost

This guide will help you configure SMTP on your Linux server to send emails from localhost using Postfix and Mailutils.

## Step 1: Update Package List

Open a terminal on your Linux server and update the package list to ensure you have the latest information:

```bash
sudo apt-get update   # For Debian/Ubuntu
# OR
sudo yum update       # For CentOS.
```

##Step 2: Install Postfix and Mailutils

Install Postfix and Mailutils by running the following command:

```bash

sudo apt-get install postfix mailutils   # For Debian/Ubuntu
# OR
sudo yum install postfix mailutils       # For CentOS
```
During the installation, you'll be prompted to select a mail configuration type. Choose "Internet Site" if you're configuring Postfix for your own domain or for general use. Follow the prompts to complete the installation. You will be asked to provide your system's mail name (e.g., example.com).


## Step 3: Configure Postfix

The primary Postfix configuration file is located at /etc/postfix/main.cf. Use a text editor to open it:

```bash

sudo nano /etc/postfix/main.cf
```
Add the following lines to the main.cf file:
```bash


relayhost = [smtp.gmail.com]:587
myhostname = your_hostname

# Location of sasl_passwd we saved
smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd

# Enables SASL authentication for postfix
smtp_sasl_auth_enable = yes
smtp_tls_security_level = encrypt

# Disallow methods that allow anonymous authentication
smtp_sasl_security_options = noanonymous
```
## Step 4: Create SASL Password File

Create a file under /etc/postfix/sasl/ with the filename sasl_passwd. 
Add the following line to this file, replacing email@gmail.com with your Gmail email address and password with your Gmail password:
```bash

[smtp.gmail.com]:587 email@gmail.com:password
```
Step 5: Start/Stop Postfix

To start Postfix, use the following command:

```bash

sudo systemctl start postfix
```
To restart Postfix, use:

```bash

sudo systemctl restart postfix
```
To stop Postfix, use:

```bash

sudo systemctl stop postfix
```
Ensure that Postfix is enabled to start at boot:

```bash

sudo systemctl enable postfix
```
## Step 6: Send a Test Email

To send a test email from the command line using Mailutils, use the mail command. Replace recipient@example.com with the recipient's email address:

```bash

echo "This is a test email" | mail -s "Test Subject" recipient@example.com
```
That's it! You've successfully configured SMTP on your localhost using Postfix and Mailutils. You can now send emails from your Linux server.
