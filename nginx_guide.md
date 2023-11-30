# Introduction

Welcome to the nginx guide! This guide will help you create your first Debian Server
and set up a sample website.

## Creating a Digital Ocean Account And Debian Droplet

You will need to create a Digital Ocean Account in order to begin.

1. Visit the Digital Ocean Official Website.
2. Click **Sign Up**.
3. Once you finish signing up, you can create your debian server by clicking the green dropdown button on the top right.
4. Select **Create Droplet**.
5. Now choose Debian as your distribution.

**Keep in mind you can't connect to it yet, the steps below will allow you to connect.**

## Creating an SSH key pair

After creating your Debian Server, you need an ssh key pair to connect to it. To do this:

1. Go to the following directory **C:Users\your-username**

2. Create an **.ssh** directory using the following command

> mkdir .ssh

3. Create your new ssh key pair using the following command

> ssh-keygen -t ed25519 -f .ssh/do-key -C "your-email-address"

## Adding SSH key pair

You wil need to login into your digital ocean for the following step:

1. Go to the Settings -> Security in your console.
2. Click **Add SSH Key** button.
3. Copy your ssh key to your clip board with the following command. (Replace user with actual name)

> Get-Content C:\Users\user-name\.ssh\do-key.pub | Set-Clipboard

## Connect to your droplet

Since you created your first droplet when you made your first Digital Ocean account, here are the following steps to achieve a connection.

1. In your Digital Ocean Terminal, copy the IP address. (You can click the IP with your mouse to copy)
2. Open your terminal.
3. Enter the ssh command below. (replace the path with your ssh key path, and your ip with the IP address you copied)

> ssh -i path-to-your-key root@your_ip

4. When it asks you to connect to the server, type **yes** and press enter.

# Create a new regular user

Now that we have connected to the server as a root user, we want to create a new regular user with
the folowing command. We also the login shell to be the bash shell and have a home directory

> useradd -ms /bin/bash **username**

We need to add a password for the user:

> passwd **username**

This will prompt you the enter the password for your user. Make sure you remember it!

Now we can add our user to the **sudo** group. This will give our user admin previlages so that they can perform higher level commands.

> usermode -aG sudo **username**

We don't want to always login as the root user, so there a few things we need to do.

We need to copy our **.ssh** directory from the root directory, and copy it into our own.

> cp - r /root/ssh /home/username

We also need to change the ownership if that directory, so we can copy it.

> sudo chown -R username:username /home/username/.ssh

To exit your server, type **exit**

Now that we have done this, we can connect to our server with the same command. This time don't use root, but your **username** instead.

> ssh -i .ssh/do-key username@ip_address

The **sshd_config** file needs to be editied so that the root user is not able to be connected to the server via ssh.

We can change our directory using the **cd** command

> cd/etc/ssh

Once you have changed directory, you can proceed to type the following command.

> sudo vim sshd_config

Look for the line which states **PermitRootLogin yes**

1. To enter insert mode, press the **I** key on your keyboard.
2. Change yes to **no**.
3. Press the **esc** key on your keyboard to exit out of insert mode.
4. Type **:wq!** to save and exit the configuration file.

Restart the ssh service with the following command:

> sudo systemctl restart ssh.service

# Creating your first Web Server

We can now officially start making our web server. To begin, we are going to need to install nginx. This is a popular web server which can be installed with the following command:

> sudo apt-get install nginx

Now that nginx is installed, we are going to have to enable it. Use the command below:

> sudo systemctl enable nginx

We are going to create our first webserver in the the **/var/www** directory where our debian server is located. Use **cd/var/www** to enter the directory.

Create a folder using the command below:

> mkdir my-site

Then create the index.html file using the **touch** command

> sudo touch my-site/index.html

Once we have this, we are going to change directory into the my-site directory. Then, we will use vim to enter it.

> sudo vim index.html

Here is the basis for our web page, copy the following code below:

    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
        }
    </style>
    </head>
    <body>
    <h1>Hello, World</h1>
    </body>
    </html>

## Creating our own server Block

We need a config file to run our server. Copy the code, we are going to need it.

    server {
    listen 80;
    listen [::]:80;

    root /var/www/my-site;

    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
    	# First attempt to serve request as file, then
    	# as directory, then fall back to displaying a 404.
    	try_files $uri $uri/ =404;
    }

}

Change directory into **/etc/nginx/sites-available** and create a new file called **my-site.conf**

Use sudo vim to enter and paste the code above.

Once you have pasted into it, we need to go change directory to **/etc/nginx/sites-enabled.**

We need to create a symbolic link towards our configuration file in the **sites-available** folder.

To do this, enter the following command:

> sudo ln -s /etc/nginx/sites-available/my-site /etc/nginx/sites-enabled

There is another file called default inside of sites-available, we will want to unlink it as we don't need it. Use the command to unlink default.

> sudo unlink default

Finally, we can test our configuration to see it is successful. Run the following command to reload nginx.

> sudo nginx -t

Then, reload nginx with this command

> sudo systemctl reload nginx.

You can test out to see if it worked. Use the curl command to see the output:

> curl **your debian ipadress**

# The End

Congratulations on making your first web server! I hope you found this guide helpful. There may be more to come in the future!
