## Server Setup

### Two things before getting started:

1. Register for a Digital Ocean account [here](https://www.digitalocean.com/)
2. Register for a .me domain name [here] (https://www.namecheap.com/)

#### First things first, Create a droplet
![image](https://user-images.githubusercontent.com/17580530/27603515-4db92654-5b44-11e7-9e43-29093e963e38.png)

#### Install the Ubuntu 16.04.2 x64 Image
![image](https://user-images.githubusercontent.com/17580530/27603543-657ad4ae-5b44-11e7-9dd0-e4d7d6de8432.png)

#### Select server size 
![image](https://user-images.githubusercontent.com/17580530/27603553-6df97590-5b44-11e7-9cca-2c9847b2840a.png)
### Select datacenter region closest to you
![image](https://user-images.githubusercontent.com/17580530/27603568-796689ae-5b44-11e7-9e2d-960e72f6322f.png)


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

`$ mkdir ~/.ssh`
`$ chmod 700 ~/.ssh`

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

### Nginx
First off, log in to your server via SSH using your newly created user. Then, update your local package index and install nginx by running: 

`$ sudo apt-get update`

`$ sudo apt-get install -y nginx`

Since you have the UFW firewall set up, you need to make sure the firewall enables connections to Nginx by running: 

`$ sudo ufw allow 'Nginx HTTP'`

**Make sure you include the single quotes**

Double check the connections to Nginx appear by running:

`$ sudo ufw status`

```bash
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

### MariaDB
Make sure everything in your local package index is update by running:

`$ sudo apt update -y`

Install "mariadb-server" by running:

`$ sudo apt install -y mariadb-server`

If a pop up asks you to set a new password for the MariaDB `root` user, go ahead and put a strong password in here and press `ENTER`

Confirm it's installed by running:

`$ mysql -V`

The output should be similar to:

`mysql  Ver 15.1 Distrib 10.1.22-MariaDB, for debian-linux-gnu (x86_64) using readline 5.2`

Start and enable MariaDB by running:

`$ sudo systemctl start mariadb.service` 

`$ sudo systemctl enable mariadb.service` 

If a service not found error appears, you may just need to restart the service by running:

`$ sudo service mysql restart`

Once MariaDB is up and running, secure the installation by running:

`$ sudo /usr/bin/mysql_secure_installation`

Enter the password you set previously for the `root` user

Hit `n` to skip having to set a new password

Hit `y` to "Remove anonymous users"

Hit `y` to "Disallow root login remotely"

Hit `y` to "Remove test database along with access"

Hit `y` to "Reload privilege tables"

Now that MariaDB is installed, you may log in as `root` locally by running:

`$ sudo mysql -u root -p`

Run `quit` to logout

### PHP
Nginx does not have native PHP processing, so you need to install "fastCGI process manager" to receive and process those requests. To do so, run:

`$ sudo apt-get install -y php-fpm php-mysql`

To then configure the processor to be more secure open the config file by running:

`$ sudo nano /etc/php/7.0/fpm/php.ini`

Hit `CTRL-w` and type `cgi.fix_pathinfo` then hit `ENTER`

Look for the line that looks like `;cgi.fix_pathinfo=1`

Be sure to **remove** the semi-colon and **set it equal to 0** which should look like this when you're done:

`cgi.fix_pathinfo=0`

Hit `CTRL-x` to close then `y` to confirm changes and `ENTER` to confirm the filename.

Restart the PHP processor by running:

`$ sudo systemctl restart php7.0-fpm`

Now, to let Nginx know to use the PHP processor you need to open the default Nginx server block config file by running:

`$ sudo nano /etc/nginx/sites-available/default`

1.	Add index.php as the first value of the index directive
2. Set the server name to point to your IP address

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name server_domain_or_IP;

    location / {
        try_files $uri $uri/ =404;
    }
```

3.	Uncomment the entire block starting with `location ~ \.php$`
4. Uncomment the entire block starting with `location ~ /\.ht`

```bash
location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
```

Save and Close the file

Hit `CTRL-x` to close then `y` to confirm changes and `ENTER` to confirm the filename.

Test there aren't any syntax errors by running:

`$ sudo nginx -t`

If there are no errors, reload Nginx to make the final changes

`$ sudo systemctl reload nginx`

Once this is reloaded, you should have a full LEMP stack completely set up on your server.

## Secure Nginx with SSL
#### Prerequisites
You must own a registered domain name for this process. If you do not wish to register a domain name, you can follow [this guide](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-in-ubuntu-16-04) to create a self-signed SSL certificate for Nginx.

### Install Certbot
Add the certbot repository with:

`$ sudo add-apt-repository ppa:certbot/certbot`

Hit `ENTER` to accept. Then update your local package index with:

`$ sudo apt-get update`

Then, install Certbot with:

`$ sudo apt-get install certbot`

### Obtain an SSL Certificate Using Webroot
Webroot needs to place a file in the `/.well-known` directory in your document root. Make sure this directory is accessible by running:

`$ sudo nano /etc/nginx/sites-available/default`

Just under the `location ~ /\.ht` block, add this block of code:

```bash
		location ~ /.well-known {
                allow all;
        }
```

Save and Close the file

Hit `CTRL-x` to close then `y` to confirm changes and `ENTER` to confirm the filename.

Test there aren't any syntax errors by running:

`$ sudo nginx -t`

Then restart Nginx with:

`$ sudo systemctl restart nginx`

Next, use Webroot to request an SSL certificate with the domains you own by running:

`$ sudo certbot certonly --webroot --webroot-path=/var/www/html -d <YOUR_DOMAIN>.com`

You may be asked how you would like to authenticate with the "ACME CA." Enter `2` to place files in your webroot directory.

Enter your email address and follow the propmts to agree to the terms of service.

You should see a success message that looks like:

```bash
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   **/etc/letsencrypt/live/<YOUR_DOMAIN>.com**/fullchain.pem. Your cert
   will expire on **2017-07-26**. To obtain a new or tweaked version of
   this certificate in the future, simply run certbot again. To
   non-interactively renew *all* of your certificates, run "certbot
   renew"
 - If you lose your account credentials, you can recover through
   e-mails sent to sammy@example.com.
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```


**Safely note down somewhere the path and expiration date that is surrounded by the asterisks ** above.**

Check that your certificate files exist by running:

`$ sudo ls -l /etc/letsencrypt/live/<YOUR_DOMAIN>.com`

You should see these 4 files:

IMAGE

To increase security, generate a Diffie-Hellman 2048-bit group by running:

`$ sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048`

Now that you have the SSL certificates, Nginx needs to know to use it by first creating a config file pointing to the SSL key and certificate by running:

`$ sudo nano /etc/nginx/snippets/ssl-<YOUR_DOMAIN>.com.conf`

Then add these 2 lines to set our `ssl_certificate` variable to our full certificate file and the `ssl_certificate_key` to the privkey file.

* `ssl_certificate /etc/letsencrypt/live/<YOUR_DOMAIN>.com/fullchain.pem;`
* `ssl_certificate_key /etc/letsencrypt/live/<YOUR_DOMAIN>.com/privkey.pem;`

Save and Close the file

Hit `CTRL-x` to close then `y` to confirm changes and `ENTER` to confirm the filename.

Next, to set Nginx with a strong SSL cipher suite, run:

`$ sudo nano /etc/nginx/snippets/ssl-params.conf`

Then simply add the following code:

```bash
# from https://cipherli.st/
# and https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html

ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_prefer_server_ciphers on;
ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
ssl_ecdh_curve secp384r1;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
# disable HSTS header for now
#add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;

ssl_dhparam /etc/ssl/certs/dhparam.pem;
```

Save and Close the file

Hit `CTRL-x` to close then `y` to confirm changes and `ENTER` to confirm the filename.

Then, we again need to let Nginx know to use SSL.

Start by backing up our server block config file:

`$ sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak`

Open the regular file to adjust:

`$ sudo nano /etc/nginx/sites-available/default`

In order to redirect unencrypted HTTP requests to HTTPS, first start by adding a `server_name` directive and a `return 301` in the initial server block:

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name <YOUR_DOMAIN>.com;
    return 301 https://$server_name$request_uri;
}
```

**Be sure to close off that server block**

Then open up a new server block just underneath this one to hold the rest of the configuration and add the following changes:

```bash
server {

    # SSL configuration

    listen 443 ssl http2 default_server;
    listen [::]:443 ssl http2 default_server;
    include snippets/ssl-<YOUR_DOMAIN>.com.conf;
    include snippets/ssl-params.conf;
    
    ...
```

Then look for the `server_name` variable within this SSL block and make sure it is set to `<YOUR_DOMAIN>.com;`

Save and Close the file

Hit `CTRL-x` to close then `y` to confirm changes and `ENTER` to confirm the filename.

Next, adjust the UFW firewall to enable SSL traffic. Double check the firewall status with `$ sudo ufw status`

```bash
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6) 
```

To allow HTTPS traffic and delete HTTP allowance, run:

* `$ sudo ufw allow 'Nginx Full'`
* `$ sudo ufw delete allow 'Nginx HTTP'`

Run `$ sudo ufw status` to see the status:

```bash
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx Full                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx Full (v6)            ALLOW       Anywhere (v6)             
```

Now, enable the changes in Nginx by first checking for any errors:

`$ sudo nginx -t`

If everything works you should see the output:

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

Next, restart Nginx with:

`$ sudo systemctl restart nginx`

If successful, you can test your domain in a browser and notice that it is responding via HTTPS.

Lastly, by default, SSL certficates are only valid for 90 days. To set up auto-renewal, first:

`$ sudo crontab -e`

Then add the following line to set up auto-renewal for your certificates.

`15 3 * * * /usr/bin/certbot renew --quiet --renew-hook "/bin/systemctl reload nginx"`

Save and Close the file

Hit `CTRL-x` to close then `y` to confirm changes and `ENTER` to confirm the filename.

## Create a MySQL(MariaDB) Database for Wordpress
If you're logged in to your server through your own sudo user, log in to MariaDB by running:

`sudo mysql -u root -p`

Enter the `root` password for your MariaDB shell.

Then create a WordPress database by running:

**Note: All commands ran in MariaDB - *terminal will display MariaDB [(none)]>* - need a semi-colon ;**

`MariaDB [(none)]> CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;`

Next, you can create a new MySQL user for this specific database with a password, and access to this database by running:

***Change the wordpressuser to whatever username you like. Also be sure to choose a strong password for the user***

`MariaDB [(none)]> GRANT ALL ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'password';`

Now that you have access to the database and user account, let MariaDB know about the changes by running:

`MariaDB [(none)]> FLUSH PRIVILEGES;`

Then run `MariaDB [(none)]> EXIT;`

Next, you need to let Nginx know to correctly use WordPress.

Open the server block file with:

`$ sudo nano /etc/nginx/sites-available/default`

Then add the following within the SSL configuration server block just below the `location /` block.

```bash
location = /favicon.ico { log_not_found off; access_log off; }
location = /robots.txt { log_not_found off; access_log off; allow all; }
location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
        expires max;
        log_not_found off;
}
```

Then, inside of the `location /` block make the following adjustments:

```bash
location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                #try_files $uri $uri/ =404;
                try_files $uri $uri/ /index.php$is_args$args;
        }
```

Double check there aren't any errors with the configuration:

`$ sudo nginx -t`

Then reload Nginx with:

`$ sudo systemctl reload nginx`

Next, you need to install some PHP extensions for working with Wordpress.

Make sure the local package index is update:

`$ sudo apt-get update`

Then install the packages:

`$ sudo apt-get install -y php-curl php-gd php-mbstring php-mcrypt php-xml php-xmlrpc`

Once the packages are installed, restart the PHP-FPM process to use the newly installed features:

`$ sudo systemctl restart php7.0-fpm`

Now you can install WordPress.

Change into a writable directory and download the latest compressed release

* `$ cd /tmp`
* `$ curl -O https://wordpress.org/latest.tar.gz`

Extract the compressed file:

`$ tar xzvf latest.tar.gz`

Copy the sample config file to the filename WordPress reads:

`$ cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php`

Create the `upgrade` directory to avoid permissions issues when WordPress updates its software

`$ mkdir /tmp/wordpress/wp-content/upgrade`

Copy the WordPress directory into our document root `/var/www/html/`

`$ sudo cp -a /tmp/wordpress/. /var/www/html`

##### Give your user ownership over the WordPress files
`$ sudo chown -R <USERNAME>:www-data /var/www/html`

##### Give the web server group ownership over the WordPress files:
`$ sudo find /var/www/html -type d -exec chmod g+s {} \;`

##### Give group write access to `wp-content`
`$ sudo chmod g+w /var/www/html/wp-content`

##### Give web server write access to WP content
* `$ sudo chmod -R g+w /var/www/html/wp-content/themes`
* `$ sudo chmod -R g+w /var/www/html/wp-content/plugins`

#### Grab secret keys for secure installation
`$ curl -s https://api.wordpress.org/secret-key/1.1/salt/`

The resulting output should look like:

```bash
define('AUTH_KEY',         '1jl/vqfs<XhdXoAPz9 DO NOT COPY THESE VALUES c_j{iwqD^<+c9.k<J@4H');
define('SECURE_AUTH_KEY',  'E2N-h2]Dcvp+aS/p7X DO NOT COPY THESE VALUES {Ka(f;rv?Pxf})CgLi-3');
define('LOGGED_IN_KEY',    'W(50,{W^,OPB%PB<JF DO NOT COPY THESE VALUES 2;y&,2m%3]R6DUth[;88');
define('NONCE_KEY',        'll,4UC)7ua+8<!4VM+ DO NOT COPY THESE VALUES #`DXF+[$atzM7 o^-C7g');
define('AUTH_SALT',        'koMrurzOA+|L_lG}kf DO NOT COPY THESE VALUES  07VC*Lj*lD&?3w!BT#-');
define('SECURE_AUTH_SALT', 'p32*p,]z%LZ+pAu:VY DO NOT COPY THESE VALUES C-?y+K0DK_+F|0h{!_xY');
define('LOGGED_IN_SALT',   'i^/G2W7!-1H2OQ+t$3 DO NOT COPY THESE VALUES t6**bRVFSD[Hi])-qS`|');
define('NONCE_SALT',       'Q6]U:K?j4L%Z]}h^q7 DO NOT COPY THESE VALUES 1% ^qUswWgn+6&xqHN&%');
```
Be sure to copy the output from **your** terminal and paste it in the wp-config file:

`$ nano /var/www/html/wp-config.php`

Scroll down to the section that has the dummy values for our keys:

```bash
 */
define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');
```

Make sure you **delete** all of these lines and **paste** the values you copied from the previous steps.

Now go to the section at the top of this file that looks like:

```bash
/** The name of the database for WordPress */
define('DB_NAME', 'database_name_here');

/** MySQL database username */
define('DB_USER', 'username_here');

/** MySQL database password */
define('DB_PASSWORD', 'password_here');
```
And change the values of the `'DB_NAME'`, `'DB_USER'`, and `'DB_PASSWORD'` to the values you made when you created your database and MariaDB user.

Add this line underneath those define functions to give your web server permission the ability to write directly to your files:

`define('FS_METHOD', 'direct');`

Save and Close the file

Hit `CTRL-x` to close then `y` to confirm changes and `ENTER` to confirm the filename.

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