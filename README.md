# Linux_project


#  1) SMTP Configuration for Localhost

This guide will help you configure SMTP on your Linux server to send emails from localhost using Postfix and Mailutils.

## Step 1: Update Package List

Open a terminal on your Linux server and update the package list to ensure you have the latest information:

```bash
sudo apt-get update   # For Debian/Ubuntu
# OR
sudo yum update       # For CentOS.
```

##  Step 2: Install Postfix and Mailutils

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
## Step 5: Start/Stop Postfix

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

# 2) Creating a user in localhost, which should not be able to execute the sudo command.

To create a user on your localhost who cannot execute the sudo command, you'll need to perform the following steps. Please note that you should have administrative privileges to create a new user or modify user permissions.

  1.  Open a terminal on your localhost.

  2.  Use the adduser or useradd command to create a new user. Replace <username> with the desired username:
    
```bash
sudo adduser <username>
```
For example, to create a user named "john":

```bash

sudo adduser john
```
3. Follow the prompts to set the user's password and provide additional information as needed.

4. By default, on many Linux distributions, a newly created user is not added to the sudo group and cannot execute sudo commands. You can double-check this by running:

```bash

groups <username>
```

Replace <username> with the actual username. The output should not include the sudo group.

5. Now, try to execute a sudo command with the newly created user:

```bash

    sudo any_command
```

    You should receive an error message indicating that the user is not in the sudoers file. This confirms that the user cannot execute sudo commands.

Your new user has been created without sudo privileges, meaning they cannot execute administrative commands using sudo.


# 3) Configure your system in such a way that when a user type and executes a describe command from anywhere of the system it must list all the files and folders of the user's current directory.


1) Open terminal on your localhost
2) Type the following command
3) Open .bashrc ,use following command to open .bashrc file
   ```bash
   vim .bashrc   #or  nano .bashrc
   ```
5) Add following line inside the .bashrc script

 ```bash

 alias describe='ls -l'

```
Now describe will work as ls and list all the files and folders present inside that directory


# 4) Users can put a compressed file at any path of the linux file system. The name of the file will be research and the extension will be of compression type, example for gzip type extension will be .gz.

1) Open your terminal on localhost
2) Use following command to find and decompressed the compressed file
   
 ```bash
    find / -name "research.gz" -exec gunzip {} \; 
```


# 5) Configure your system in such a way that any user of your system creates a file then there should not be permission to do any activity in that file.

 Open a terminal on your system.

To restrict all permissions for others, set the umask value to 0777 into your shell file (.bashrc)

```bash

umask 0777
```
This setting will ensure that when any user creates a file, the default permissions for others will be restricted, and they won't have any access to the file.

Save the changes and either log out and back in or run the following command to apply the new umask setting immediately:

```bash

    source ~/.bashrc  # or the appropriate profile file
```
Now, when any user creates a new file, it will have permissions that prevent others from accessing it in any way.

For example, if a user creates a file using the touch command:

```bash

touch myfile.txt
```
The permissions for myfile.txt will be such that only the file owner (the user who created it) will have read and write access, while all others will have no permissions:

```bash

------- 1 username username 0 Sep 18 15:30 myfile.txt
```
Please note that while this restricts others from accessing the file, the file owner can still modify its permissions if needed.


# 6) Create a service with the name showtime , after starting the service, every minute it should print the current time in a file in the user home directory.

To create a systemd service named "showtime" that prints the current time to a file in the user's home directory every minute, follow these steps:

  1)  Create a Bash script that prints the current time and saves it to a file.

```bash
#!/bin/bash
while true; do
    date | awk '{print $4}'  >> "$HOME/showtime.txt"
    sleep 60  # Sleep for 60 seconds (1 minute)
done

```

2) Save this script in a location where the user has write access (e.g., /home/showtime.sh) and make it executable:

```bash
chmod +x /home/<username>/showtime.sh

```
3) Create a systemd service unit file for "showtime." You can create a new file, such as /etc/systemd/system/showtime.service, and add the following content to it:


```bash
[Unit]
Description=Showtime Service

[Service]
Type=simple
ExecStart=/home/showtime.sh
User=<username>
Restart=always

[Install]
WantedBy=multi-user.target

```
Replace <username> with the actual username.

4) Reload the systemd manager configuration to update the available services:

```bash
sudo systemctl daemon-reload

```
5) Enable the "showtime" service to start on boot:

```bash
sudo systemctl enable showtime.service

```
6) Start the "showtime" service:

```bash

    sudo systemctl start showtime.service
```
The "showtime" service is now running and will print the current time to the showtime.txt file in the user's home directory every minute. You can check the output in /home/<username>/showtime.txt.
