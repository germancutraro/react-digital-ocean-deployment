# DigitalOcean CRA / Nginx & Ubuntu deployment:

- **Nginx:** Lightweight and high performance Web server or Reverse Proxy. It is mainly used to act as a reverse proxy to quickly serve static files and send requests to proxied web servers or act as a load balancer.

- **Ubuntu:** A very popular Linux Distro. Version 16.04 is the latest Long Term Support Version and will be until 2021.

- **DigitalOcean:** A Cloud Infrastructure Company that allows you to create a droplet/server in the “cloud”. It starts off at \$5/month, making it very affordable.

**IMPORTANT: everything inside [HERE] is relative of you.**

### Open you CMD and type

`ssh root@[DROPPLET_IP]`

#### Update Ubuntu repos

`sudo apt-update`

`sudo apt-upgrade`

Next, we need to install Node.js and NPM

`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash`

`nvm install node`

`source ~/.bashrc`

#### If you want you can install yarn like this:

`npm i -g yarn`

#### The next step is creating our folder to hold inside our build directory (you can name it whatever you want)

`sudo mkdir /var/www`

`cd /var/www`

Since you wouldn’t want to use sudo every time you interact with files in /var/www or /www, let’s give your user privileges to these folders. Constantly using sudo increases the chances of accidentally trashing your system.

`sudo gpasswd -a "$USER" www-data`

`sudo chown -R "$USER":www-data /var/www`

`find /var/www -type f -exec chmod 0660 {} \;`

`sudo find /var/www -type d -exec chmod 2770 {} \;`

#### Next, we can clone our repo

`git clone [YOUR_REPO_URL]`

`cd [YOUR_REPO_NAME]`

`sudo yarn`

`sudo yarn build`

#### if you can´t do it, just create a ssh key like this

`ssh-keygen`

and then do `vi [YOUR_SSH_PUBLIC_PATH]`, copy the whole text and add in into github ssh keys.

#### Install and configure Nginx

`sudo apt-get install nginx`

`cd /etc/nginx/sites-available/`

`nano default` and delete everything there, you can select all the text using `shift + keys` and cut it using `Ctrl + K`

and paste:

`server {`

`listen 80 default_server;`

`root /var/www/[YOUR_REPO_FOLDER_NAME]/build;`

`server_name [YOUR_DOMAIN.COM] [WWW.YOUR_OTHER_DOMAIN.COM];`

`index index.html;`

`location / {`

`try_files $uri /index.html;`

`}`

`}`

You can save it by pressing `Ctrl + O` and then `Ctrl + X`, and finally `enter`

`sudo service nginx start`

`sudo service nginx restart`

#### Basic Firewall Set Up

`sudo ufw allow OpenSSH`

`sudo ufw enable`

`sudo ufw status`

`sudo ufw allow 'Nginx HTTP'`

#### Set SSL Certbot

`sudo add-apt-repository ppa:certbot/certbot`

`sudo apt-get update`

`sudo apt install python-certbot-nginx`

`sudo certbot --nginx -d [YOUR_DOMAIN.COM] -d [WWW.YOUR OTHER_DOMAIN.COM]`

press enter, write your email, and the choose the option `2``

Let's Encrypt's certificates are only valid for 90 days. This is to encourage users to automate their certificate renewal process. The Certbot package we installed takes care of this for us by running certbot renew twice a day via a systemd timer. On non-systemd distributions this functionality is provided by a script placed in /etc/cron.d. This task runs twice a day and will renew any certificate that's within thirty days of expiration.
To test the renewal process, you can do a dry run with Certbot:

`sudo certbot renew --dry-run`

If you see no errors, you're all set. When necessary, Certbot will renew your certificates and reload Nginx to pick up the changes. If the automated renewal process ever fails, Let’s Encrypt will send a message to the email you specified, warning you when your certificate is about to expire.

#### Finally

`sudo service nginx start`

`sudo service nginx restart`

## Updates

When you push your new code inside your master branch, for example, you need to go inside your folder.

`cd /var/www/[YOUR_REPO_FOLDER_NAME]`

`git pull`

`yarn` (only if you installed a new package)

`yarn build`

`sudo service nginx start`

`sudo service nginx restart`

and you finish!
