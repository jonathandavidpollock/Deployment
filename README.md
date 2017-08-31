
# Getting Started with LEMP and Wordpess

#### This codebase covers how to set up an Ubuntu 16.04 server with the LEMP stack: Linux, Nginx, MariaDB, and PHP. Once you have LEMP set up properly, you can user SCP (Secure Copy) Deployment to transer files from your local environment to a remote environment. 

- Use this link to set up your remote environment: [Set Up LEMP Server](https://google.com)

## Local Installation
#### Prerequisites
For this project, you will need a basic understand of bash commands as well as an understanding of SSH. Let's start by creating a directory on our desktop and by going into that directory.

```
mkdir ~/Desktop/LEMP
cd ~/Desktop/LEMP
```

**NOTES:** 

You may also clone this repo on local machine and copy my Wordpress version. However, my version is most likely out of date. I recommend visiting [WordPress.org](https://wordpress.org/) and download the latest version of WordPress. If you download the files from wordpress you will need to unzip them.

## Server Setup
[This document](https://google.com) goes through the entire process of setting up an Ubuntu 16.04 with a full LEMP stack and HTTPS configuration. This copy comes directly from Digital ocean which has fantastic documentation.

## Deployment (Secure Copy Deployment)

#### Prerequisites
To get started, you will need the following 3 items:
	
	• An Ubuntu 16.04 server
	• A sudo user 
	• Your domain or public IP address
	
Don't have these 3 items?**  **[Set Up LEMP Server](https://github.com/eheckard23/Server_Stack/blob/master/setup.md)

#### Copy the Humans.txt file to your local machine and 

* Open the file `humans.txt` located on the master branch of this repository
* Save the file to your local machine.
* Open terminal and navigate to the parent directory of `humans.txt`


```
cat humans.txt
```


It should output something like this.

```text
/* TEAM */
        Chef:Juanjo Bernabeu
        Contact: hello [at] humanstxt.org
        Twitter: @juanjobernabeu
        From:Barcelona, Catalonia, Spain

        UI developer: Maria Macias
        Twitter: @maria_ux
        From:Barcelona, Catalonia, Spain

        One eyed illustrator: Carlos Mañas
        Twitter: @oneeyedman
        From:Madrid, Spain

        Standard Man: Abel Cabans
        Twitter: @abelcabans
        From:Barcelona, Catalonia, Spain
        
        ...
```
If your file reads like this, you are ready for the next step.

#### Securely Copy the File to the Web Server


`$ scp humans.txt <USERNAME>@<YOUR_IP_ADDRESS>:/var/www/html`


Next, ssh into your server using your `sudo` user. Find the humans.txt in your html directory.

`$ ls /var/www/html`

If you see the file in your html directory, you have successfully deployed using SCP.
