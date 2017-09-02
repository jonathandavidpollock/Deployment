## Server Setup

### Two things before getting started:

1. Register for a Digital Ocean account [here](https://www.digitalocean.com/)
2. Register for a .me domain name [here] (https://www.namecheap.com/)

#### First things first, Create a droplet
Create an Ubuntu 16.04.2 x64 Image with the server size you want. Next, select the data center closest to you.

![image](https://user-images.githubusercontent.com/17580530/27603515-4db92654-5b44-11e7-9e43-29093e963e38.png)

#### Add an SSH Key

Next we will add an SSH Key. To generate a key pair enter the command below in terminal.

```
$ ssh-keygen -t rsa
```

The command will output the following prompt.


```
Enter file in which to save the key (/Users/<YOUR_USER>/.ssh/id_rsa)
```

Hit enter to save the key. Once complete, the output should look like:

```bash
ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/demo/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /demo/.ssh/id_rsa.
Your public key has been saved in /demo/.ssh/id_rsa.pub.
The key fingerprint is:
4a:dd:0a:c6:35:4e:3f:ed:27:38:8c:74:44:4d:93:67 demo@a
The key's randomart image is:
+--[ RSA 2048]----+
|          .oo.   |
|         .  o.E  |
|        + .  o   |
|     . = = .     |
|      = S = .    |
|     o + = +     |
|      . o + o .  |
|           . o   |
|                 |
+-----------------+
```



### Copy the public SSH key to your server
Copy the public key from the location you saved it in the previous step. In this case, from terminal, run:

```
$ cat ~/.ssh/id_rsa.pub
```

Copy the output. Go back to your Digital Ocean server setup page, and enter the name of your local machine in the "Enter Name" field. Next, paste the public key into the "Public SSH Key" field and hit "CREATE SSH KEY."


### Login to the Root User of your Web Server 

	
In terminal, run the following command:

Note: If you have connected your IP address to your domain, you may access your server through the domain instead of the IP address. Simply, replace the `<YOUR_IP_ADDRESS>` with you domain below.

```
$ ssh root@<YOUR_IP_ADDRESS>
```

You will be asked to enter your password.

### Creating another User on our Server
We never use the Root user for anything. We use a command called sudo to temporarily give us Root privilages. Root can cause a lot of damage. Sudo is how we protect ourselves.

**Note: Sudo stand for Super User Do**

Let's add a user by using the command below. I recommend making the username memorable, like your name.

```
adduser <USERNAME>
```

Enter a strong password! However, later we will remove password login.

### Give <USERNAME> Root privilages

 Let's add <USERNAME> to the "sudo" group by typing the following command in terminal.

```
# usermod -aG sudo <USERNAME>
```

### Public Key Authentication

Logout of your server using the `logout` command and run the following command on your local machine:

```
$ ssh-keygen
```

Hit Enter to save the key into the path `(/Users/<YOUR_USER>/.ssh/id_rsa)`. It will then prompt you for a passphrase. If you want more security, create a passphrase. However, you will need this passphrase to login to the server every time you want to access it.


#### Copy the Public Key from Your Local Machine to Your Web Server

Open terminal and run the following command. 

```
$ cat ~/.ssh/id_rsa.pub
```

Copy the entire output. Next let's add this copied public key to a specific file in your web server's home directory. Let's do that now.

- Log in to your server as **ROOT**
- Switch to the **USERNAME** you created with:

```
su - <USERNAME>
```

Make a new directory called `.ssh` and give **only the root user** full permissions by running:

```
$ mkdir ~/.ssh
$ chmod 700 ~/.ssh
```

Next, run the following command to create a file and open it in nano.

```
$ nano ~/.ssh/authorized_keys
```

Paste the public key you copied when you ran `$ cat ~/.ssh/id_rsa.pub` on your local machine.

	Hit `CTRL-x` to exit
	Hit `y` to save
	Hit `ENTER` to confirm the file name

Now give **only the root user** read and write permission to the `authorized_keys` file by running:

`$ chmod 600 ~/.ssh/authorized_keys`

Exit as **root** by running:

`$ exit`

Now you can use SSH to log in as your new user by running:

```
$ ssh <USERNAME>@<YOUR_IP_ADDRESS>
```



## Install Linux, Nginx, MariaDB, and PHP

Follow this tutorial to set up the LEMP stack.

## Install Wordpress

Follow this tutorial to set up Wordpress on the LEMP stack.



#### Completing the Installation
To complete installation, visit your domain or IP address in the browser and follow the prompts until you access your the wordpress configuration guide. You will need to select a primary language and create an account.




#### Updating your Wordpress Server
Whenever you need to upgrade your WordPress, log in to your server with your sudo user and run:

```
$ sudo chown -R www-data /var/www/html
```

Then lock the permissions back to for safety:

```
$ sudo chown -R <USERNAME> /var/www/html
```

Logging into your server and using chown is the most secure way to update your Wordpress site. I do not recommend you update your site through the GUI.