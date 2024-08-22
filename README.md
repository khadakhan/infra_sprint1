# About project infra_sprint1 (kittygram)
Kittygram is a social network for sharing photos of your favorite pets. This project consists of a backend application on Django and a frontend application on React.

# Deploy a project to a server:
To connect to your server via SSH, you will need the following data:
* Get a domain name that will allow the application to be accessed. (For example, kittydo.zapto.org)
* login to connect to the server;
* ip - the server IP address, and preferably a domain name;
* passphrase - the password for the private SSH key;
* a file with the .pub extension is the public SSH key;
* a file without an extension is the private SSH key.

If you are running Linux or macOS, you probably have an SSH client, it is built into the terminal by default.
For Windows users, we recommend installing Git Bash version 2.36.0 or higher. The SSH client is built into this terminal.
To make sure you have an SSH client, open the terminal and enter the command:
```
ssh -V
```
To connect to the server, use the command:
```
ssh -i <path to_file_with_SSH_key>/<name of private SSH key> file login@ip
```
## Connect a remote server to your GitHub account
Install git on the server using the command:
```
sudo apt install <package_name>
```
or check that git is already installed:
```
sudo apt update
git --version
```

While on the server, generate a pair of SSH keys, output the public key to the console, copy it and write it to your GitHub account settings.
(you can follow [the instructions](https://file.notion.so/f/f/1c00a917-6a50-4d8a-a607-d24904249cd0/cce9043b-0706-44bc-9730-53923cef05ab/Настройка_SSH_для_GitHub.pdf?table=block&id=2ccd2557-d585-4e6d-980b-b0a3319229cd&spaceId=1c00a917-6a50-4d8a-a607-d24904249cd0&expirationTimestamp=1724284800000&signature=FpJxT18lS-aTUEcG6PS41N-sm05oMkufeXhAAnlMZjc&downloadName=Настройка+SSH+для+GitHub.pdf))

### Клонируйте код приложения с GitHub на сервер:
Находясь на сервере, выполните команду:
```
git clone git@github.com:Ваш_аккаунт/taski.git
```

1. Install the necessary components on the server: Python interpreter, pip package manager, utility for creating a virtual environment venv. If your server is running Ubuntu, the third version of Python interpreter is pre-installed. Make sure of this - while on the server, run the command:

```
python3 -V
```
Next, install the package manager and utility for creating a virtual environment on the server. This can be done with one command, listing all the packages required for installation:

```
sudo apt install python3-pip python3-venv -y
```

2. Install dependencies from the requirements.txt file:
```
# Go to the project's backend application directory.
cd taski/backend/
# Create a virtual environment.
python3 -m venv venv
# Activate the virtual environment.
source venv/bin/activate
# Update pip in the virtual environment
pip install --upgrade pip
# Install dependencies.
pip install -r requirements.txt
```

3. Run migrations and create a superuser:
```
# Apply migrations.
python manage.py migrate
# Create a superuser.
python manage.py createsuperuser
```

## Launching the backend
To launch the project via the external interface, you need to explicitly add your server's IP address to the ALLOWED_HOSTS "allowed hosts list" in the settings.py file:
```
# Open the settings.py file
nano settings.py
```
Add to the ALLOWED_HOSTS list:
your server's external IP address to access the application via the external interface,
the addresses 127.0.0.1 and localhost to access the application via the internal interface:
```
# Replace xxx.xxx.xxx.xxx with your server's IP.
ALLOWED_HOSTS = ['xxx.xxx.xxx.xxx', '127.0.0.1', 'localhost']
```
Start the server with the command:
```
python manage.py runserver 0:9000
```
Now you can go to the admin panel using the link http://your_public_IP:8000/admin/ and log in as the superuser you created, or you can follow the link http://your_public_IP:8000/api/ to open the built-in Browsable API debugging interface.
Stop the backend server.

## Launching the frontend
The Taski project's frontend application is written in React. To launch it, you need to:
* Install the npm package manager on the server. The easiest way to get started with npm is to install Node.js on the server, which comes with the package manager. While on the server, run the command from any directory (copy it entirely and paste it into the terminal):
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt install -y nodejs
```
After installing Node.js, the server will most likely ask you to reboot the operating system again. Reboot it and move on.
Next, make sure that the npm package manager is also installed. Run the command:
```
npm -v
```
The response should display the version of the package manager.
* Install dependencies for the frontend application. Go to the taski/frontend/ directory and run the command:
```
npm i
```
Run the application with the command:
```
npm run start
```
check by the link http://your_public_IP:3000 and stop the frontend server.

## Install and run Gunicorn
On the remote server, with the Taski project virtual environment activated, install the gunicorn package:
```
pip install gunicorn==20.1.0
```
Go to the directory with the manage.py file (infra_sprint1/backend) and run Gunicorn:
```
gunicorn --bind 0.0.0.0:9000 kittygram_backend.wsgi
```
Create a unit for Gunicorn. In the /etc/systemd/system/ directory, create the gunicorn_kittygram.service file and open it in Nano. To create a file in the system folder, you need administrator rights, which are provided by the sudo command. Run the command:
```
sudo nano /etc/systemd/system/gunicorn_kittygram.service

```
Describe the process configuration in the gunicorn_kittygram.service file.

Substitute your data into the code from the listing, add this code without comments to the gunicorn_kittygram.service file and save the changes:
```
[Unit]
# This is a text description of the unit, an explanation for the developer.
Description=gunicorn_kittygram daemon

# Condition: when starting the operating system, start the process only after
# the operating system has loaded and configured a network connection.
# Link to documentation with possible value options
# https://systemd.io/NETWORK_ONLINE/
After=network.target

[Service]
# On whose behalf the launch will occur:
# specify the user name under which you connected to the server via ssh.
User=yc-user

# Path to the project directory:
# /home/<user-name-in-the-system>/
# <directory-with-the-project>/<directory-with-the-manage.py-file>/.
# For example:
WorkingDirectory=/home/yc-user/infra_sprint1/backend/

# The command you ran manually will now be run by systemd:
# /home/<user-name-on-the-system>/
# <directory-with-the-project>/<path-to-gunicorn-in-the-virtual-environment> --bind 0.0.0.0:9000 kittygram_backend.wsgi
ExecStart=/home/yc-user/taski/backend/venv/bin/gunicorn --bind 0.0.0.0:9000 kittygram_backend.wsgi

[Install]
# This parameter specifies the option to start the process.
# The <multi-user.target> value specifies that systemd will start a process that is
# accessible to all users and without a graphical interface.
WantedBy=multi-user.target
```
To view processes, use the command:
```
sudo systemctl
```
To start, stop, or restart a process, use the sudo systemctl command with the start, stop, or restart parameters.
Start the gunicorn_kittygram.service process:
```
sudo systemctl start gunicorn_kittygram
```
Add the Gunicorn process to the startup list of the operating system on the remote server with an additional command:
```
sudo systemctl enable gunicorn_kittygram
```
After a short wait, you can check the functionality of the running daemon. To do this, use the command:
```
sudo systemctl status gunicorn_kittygram
```
You have installed and configured the Gunicorn WSGI server: it will start when the operating system on the remote server starts, and if problems arise, it will automatically reboot. You can also manage it manually, for this there are commands sudo systemctl start/stop/restart gunicorn.

## Nginx web and reverse proxy server: installation and configuration
From the remote server, run the command from any directory:
```
sudo apt install nginx -y
```
Next, the server will most likely ask you to reboot the operating system - do this. And then start Nginx with the command:
```
sudo systemctl start nginx
```
You need to leave the ability to send requests only to some ports, for example:
* 80 - HTTP;
* 443 - HTTPS;
* 22 - SSH.
The Ubuntu operating system comes with a firewall ufw, but it is disabled by default. The task is to specify the ports that need to be opened and enable the program. Tell the firewall which ports should remain open. To do this, run two commands on the server one after the other:
```
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
```
The sudo ufw allow OpenSSH command enables permission for port 22 — this is the standard port for connecting via SSH. If this port is not opened, then access to the remote server will be closed immediately after turning on the firewall, and you will no longer be able to get there. Now turn on the firewall:
```
sudo ufw enable
```
Next, check the changes you made:
```
sudo ufw status
```
The ufw firewall will tell you that it is “active” and allows you to receive requests on the ports you specified.

Collecting statics for the frontend application. Go to the infra_sprint1/frontend directory and run the command:
```
npm run build
```
For Nginx to serve static files, it needs to know where they are. The web server has a system directory that it uses by default to access static files — /var/www/. Copy the contents of the .../frontend/build/ folder to this directory:
```
# The cp command — copy, the -r switch — recursively, including nested folders and files.
sudo cp -r /home/yc-user/infra_sprint1/frontend/build/. /var/www/kittygram/
# The dot after build is important — the contents of the directory will be copied.
```
We describe the settings for working with the statics of the frontend application:
```
sudo nano /etc/nginx/sites-enabled/default
```
This file already contains the initial settings for the web server. That is why when accessing the server's IP address, the Nginx start page is displayed.

Delete all the settings from the file, write down and save the new ones. We set up proxying of requests right away:
```
server {
    server_name 158.160.91.227 kittydo.zapto.org;
    
    location / {
        root   /var/www/kittygram;
        index  index.html index.htm;
        try_files $uri /index.html;
    }

    location /api/ {
        client_max_body_size 20M;
        proxy_pass http://127.0.0.1:9000;
    }

    location /admin/ {
        client_max_body_size 20M;
        proxy_pass http://127.0.0.1:9000;
    }

    location /media/ {
        root /var/www/kittygram;
}
```
Save the changes, check and reload the web server configuration:
```
sudo nginx -t
sudo systemctl reload nginx
```
To prepare the backend application for collecting statics, in the settings.py file, specify the directory where this static should be stored.

Using the Nano editor, open the settings.py file, specify a new value for the STATIC_URL constant and create a STATIC_ROOT constant:
```
# Replace the default value 'static' with 'static_backend',
# to avoid a conflict of requests to the frontend and backend statics.
STATIC_URL = '/static_backend/'
# Specify the directory where the backend application should store the statics.
STATIC_ROOT = BASE_DIR / 'static_backend'
```
On the remote server, with the virtual environment activated, go to the directory with the manage.py file and run the command:
```
python manage.py collectstatic
```
A static_backend/ directory will be created in the infra_sprint1/backend/ project directory with all the static files of the backend application. This terminal response confirms this:
```
161 static files copied to '/home/yc-user/infra_sprint1/backend/static_backend'.
```
Go to the root of the Infra_sprint1 project and copy the static_backend/ directory to the /var/www/kittygram/ directory. To do this, run the command:
```
sudo cp -r /home/yc-user/infra_sprint1/backend/static_backend/ /var/www/kittygram/
```
For the changes in the settings.py file to take effect, restart Gunicorn:
```
sudo systemctl restart gunicorn
```

## Adding a domain name to Django settings
On the server, go to the /infra_sprint1/backend/kittygram_backend directory, use Nano to open the settings.py file and add the resulting domain name to the ALLOWED_HOSTS list:
```
ALLOWED_HOSTS = ['xxx.xxx.xxx.xxx', '127.0.0.1', 'localhost', 'your-domain']
# Replace xxx.xxx.xxx.xxx with your server's IP.
# The domain is entered in the format 'project.hopto.org'.
```
Save the changes and restart Gunicorn with the command sudo systemctl restart gunicorn for the changes to take effect.

Edit the Nginx configuration file. Open it in Nano:
```
sudo nano /etc/nginx/sites-enabled/default
```
Find the line in the file that begins with server_name. The IP is already specified there, add the domain name assigned to you after a space:
```
server {
...
    server_name <your-ip> <your-domain>;
...
}
```
После этого проверьте конфигурацию sudo nginx -t и перезагрузите её командой sudo systemctl reload nginx, чтобы изменения вступили в силу.

## Encryption. HTTPS
You can install an SSL certificate from Let’s Encrypt on a web server manually, but you can also automate this process. To do this, you will need special software, for example, the certbot package - it is provided by the Let’s Encrypt certification center specifically for Linux systems.
To install certbot, you will need the snap package manager. Install it with the command:
```
sudo apt install snapd
```
Then the server will most likely ask you to reboot the operating system. Do this, and then sequentially run the commands:
```
# Installing and updating dependencies for the snap package manager.
sudo snap install core; sudo snap refresh core
# If the dependencies are successfully installed, the terminal will display:
# core 16-2.58.2 from Canonical✓ installed

# Installing the certbot package.
sudo snap install --classic certbot
# If the package is successfully installed, the terminal will display:
# certbot 2.3.0 from Certbot Project (certbot-eff✓) installed

# Create a link to certbot in the system directory,
# so that a user with administrator rights has access to this package.
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
To start the process of obtaining a certificate, enter the command:
```
sudo certbot --nginx
```
Then the system will notify you that the account has been registered and ask you to specify the names for which you would like to activate HTTPS:
```
Account registered.

Which names would you like to activate HTTPS for?
We recommend selecting either all domains, or all domains in a VirtualHost/server block.

1: <your_project_domain_name>

Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel):
```
Enter 1 or leave nothing and press Enter.
After that, certbot will send your data to the Let's Encrypt server, where a certificate will be issued, which will be automatically saved on your server in the system directory /etc/ssl/. The Nginx configuration will also be automatically changed: new settings will be added to the /etc/nginx/sites-enabled/default file and the paths to the certificate will be specified.
Open the /etc/nginx/sites-enabled/default file and make sure of this:
```
server {
    server_name 158.160.91.227 kittydo.zapto.org;

    location / {
        root   /var/www/kittygram;
        index  index.html index.htm;
        try_files $uri /index.html;
    }

    location /api/ {
        client_max_body_size 20M;
        proxy_pass http://127.0.0.1:9000;
    }

    location /admin/ {
        client_max_body_size 20M;
        proxy_pass http://127.0.0.1:9000;
    }

    location /media/ {
        root /var/www/kittygram;
    }

# Next come new settings and a description of the paths to the certificate.

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/kittydo.zapto.org/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/kittydo.zapto.org/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = kittydo.zapto.org) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    server_name 158.160.91.227 kittydo.zapto.org;
    listen 80;
    return 404; # managed by Certbot

}
```
Reload the Nginx configuration:
```
sudo systemctl reload nginx
```
Configuring automatic renewal of the SSL certificate. SSL certificates have a limited validity period: for example, a certificate from Let’s Encrypt is valid for 90 days. But you don’t have to renew it manually, the certbot package will do it for you if nothing has changed in its configuration.

To find out the current status of the certificate and how many days are left until its reissue, use the command:
```
sudo certbot certificates
```
The output in the terminal will be something like this:
```
Found the following certs:
  Certificate Name: ваше_доменное_имя
    Serial Number: 4f8d9d12b7f7e41124321322233db9024446372120
    Key Type: ECDSA
    Domains: ваше_доменное_имя
    Expiry Date: <дата> <время> (VALID: 89 days)
    Certificate Path: /etc/letsencrypt/live/ваше_доменное_имя/fullchain.pem
    Private Key Path: /etc/letsencrypt/live/ваше_доменное_имя/privkey.pem
```
Now make sure that the certificate will be renewed automatically:
```
sudo certbot renew --dry-run
```
If no error is displayed, then everything is fine.
You can manually renew the certificate with the command:
```
sudo certbot renew --pre-hook "service nginx stop" --post-hook "service nginx start"
```
This command will renew the certificate and restart nginx.

## Useful commands:
Stop processes:
```
pkill gunicorn (or process number)
```
what is the port busy with
```
sudo lsof -i tcp:8080
```
free the port
```
sudo fuser -k 8080/tcp
```
stop Django server
```
pkill -f runserver
```
check space on the server
```
sudo df
```
copied files to the server:
```
scp -i "C:\Dev\vm_access\yc-khadakhanoff"/yc-khadakhanoff docs \
yc-user@158.160.91.227:/home/yc-user/foodgram/docs
```

# About the author
Project author: [khadakhan](https://github.com/khadakhan/)