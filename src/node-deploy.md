# Node.js Deployment

> Steps to deploy a Node.js app to Linode using PM2, NGINX as a reverse proxy and an SSL from LetsEncrypt

## 1. Install Node/Yarn

```bash
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

sudo apt install nodejs

node --version

npm i -g yarn
```

## 2. Install Git

```bash
apt install git
```

## 3. Add SSH Key

```zsh
ssh-keygen -t rsa

cat ~/.ssh/id_rsa.pub

# Copy the public to clipboard
# Go to your github Settings > SSH and GPG keys > New SSH Key
# Give a proper name and save it
```

## 4. Clone your project from Github

There are a few ways to get your files on to the server, I would suggest using Git

```bash
mkdir /var/www
cd /var/www
git clone git:yourproject.git
```

### 5. Install dependencies and test app

```bash
cd yourproject
yarn
yarn start (or whatever your start command)
# stop app
ctrl+C
```

## 6. Setup PM2 process manager to keep your app running

```bash
yarn global add pm2
pm2 start app (or whatever your file name)

# Other pm2 commands
pm2 show app
pm2 status
pm2 restart app
pm2 stop app
pm2 logs (Show log stream)
pm2 flush (Clear logs)

# To make sure app starts when reboot
pm2 startup ubuntu
```

### You should now be able to access your app using your IP and port. Now we want to setup a firewall blocking that port and setup NGINX as a reverse proxy so we can access it directly using port 80 (http)

## 7. Setup ufw firewall

```bash
ufw enable
ufw status
ufw allow ssh (Port 22)
ufw allow http (Port 80)
ufw allow https (Port 443)
```

## 8. Install NGINX and configure

```bash
apt install nginx
rm /etc/nginx/sites-available/default
vim /etc/nginx/sites-available/default

```

Add the following to the location part of the server block

```bash
# For Node Application Deploy
server {
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:5000; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

# For Website Deploy without start script, CRA, etc...
server {
    server_name yourdomain.com www.yourdomain.com;

    root /var/www/yourproject/path/to/dist

    location / {
        try_files $uri index.html;
    }

    # If you have sitemap
    location /sitemap.xml {
        alias path/to/sitemap.xml
    }
}
```

```bash
# Check NGINX config
nginx -t

# Restart NGINX
service nginx restart
```
