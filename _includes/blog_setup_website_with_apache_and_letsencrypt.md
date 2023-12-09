# Setup a Website with Apache and Letsencrypt on Ubuntu

References:

[How to use the Apache web server to install and configure a website](https://www.techrepublic.com/article/how-to-use-the-apache-web-server-to-install-and-configure-a-website/)


[How To Use Certbot Standalone Mode to Retrieve Let's Encrypt SSL Certificates](https://www.digitalocean.com/community/tutorials/how-to-use-certbot-standalone-mode-to-retrieve-let-s-encrypt-ssl-certificates-on-ubuntu-16-04)

## Enable a Website with Apache

Installing apache on Ubuntu
```bash
sudo apt-get install apache2 -y
sudo systemctl enable apache2
```

## Create HTML files for your website
Make a directory for the source code of website `example.com` and setup permissions.
```bash
sudo mkdir -p /var/www/example-com
sudo chown -R $USER:$USER /var/www/example-com
sudo chmod -R 755 /var/www/example-com
```

Create the first HTML file for example.com
```bash
sudo touch /var/www/example-com/index.html
```

## Create an Apache configuration file for your website
Create an Apache configuration for website `example.com`.
```bash
sudo vim /etc/apache2/sites-available/example-com.conf
```

Add the following text to `example-com.conf`.
```
ServerAdmin admin@example.com # Define the email of the admin
ServerName example.com # The domain
ServerAlias www.example.com # Redirect www.example.com to example.com
DocumentRoot /var/www/example-com/ # Root dir of HTML docs.
ErrorLog ${APACHE_LOG_DIR}/error.log # Where log files are stored. Default ${APACHE_LOG_DIR} is /var/log/apache2
CustomLog ${APACHE_LOG_DIR}/access.log combined 
```

Enable your site:
```bash
sudo a2ensite test.conf
```

Reload Apache
```bash
sudo systemctl reload apache2
```

Test the syntax of Apache files
```
apachectl configtest
```

## (Optionally) Get a Cert with LetsEncrypt
Install Letsencrypt Certbot
```bash
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo ufw allow 80
```

Get a cert for website `example.com`.
```
# You might need to stop apache first, run the following to stop apache
# sudo systemctl stop apache2
sudo certbot certonly --standalone --preferred-challenges http -d example.com
# Restart apache2 with the following command
# sudo systemctl restart apache2
```

When running the command, you will be prompted to enter an email address and agree to the terms of service. After doing so, you should see a message telling you the process was successful and where your certificates are stored:
```
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your cert will
   expire on 2017-10-23. To obtain a new or tweaked version of this
   certificate in the future, simply run certbot again with the
   "certonly" option. To non-interactively renew *all* of your
   certificates, run "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

Check the files are generated properly:
``` bash
sudo ls /etc/letsencrypt/live/example.com
# Output should be: cert.pem  chain.pem  fullchain.pem  privkey.pem
```

Add cert data to your website's configuration file (`/etc/apache2/sites-available/example-com.conf`):
```
SSLEngine on
SSLCertificateFile      /etc/letsencrypt/live/example.com/cert.pem
SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
SSLCertificateChainFile /etc/letsencrypt/live/example.com/fullchain.pem
```

Renew cert for a website:
```bash
sudo certbot certonly --force-renew --preferred-challenges http -d example.com
```

When prompted `How would you like to authenticate with the ACME CA?`, choose `Spin up a temporary webserver (standalone)`

Once again, you might have to stop apache2 temporarily.