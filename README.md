

## Ubuntu Local Server Installation instructions

### ssh to ubuntu
Run the following command (replace username with the actual user account on the remote machine):
    
1. Find username

```sh
whoami (username)
```
2. Find IP

```sh
hostname -I
```

```sh
ssh <username>@<IP>
```

### 1. Install Ubuntu (Give username and Password)

If any WSL Error ? (error code 0x80370114)
Enable WSL Feature

Open PowerShell as Administrator:

Right-click the Start menu and select Windows PowerShell (Run as administrator).
Run the following commands to enable WSL and Virtual Machine Platform (One by one):

```sh
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
dism.exe /online /enable-feature /featurename:Microsoft-Hyper-V-All /all /norestart
```

    Restart your PC after running the commands.


### 2. Update and Upgrade linux machine and install node

```sh
sudo apt update
```

```sh
sudo apt upgrade
```

```sh
sudo apt install -y git htop wget
```

#### 3.4 Install node

#### 3.5 Check nodejs installed
```sh
node --version
```


### 4. Clone nodejs-ssl-server repository

```sh
cd /home/ubuntu
```

```sh
git clone [repo url]
```

```sh
cd [repo]
```

```sh
npm install
```

### 6. Install pm2
```sh
npm install -g pm2 # may require sudo
```

### 7. Starting the app with pm2 (Run nodejs in background and when server restart)
```sh
pm2 start app.js
```
```sh
pm2 save
```

#### 7.1 IMPORTANT: If you want pm2 to start on system boot
```sh
pm2 startup # starts pm2 on computer boot
```

### 8. FREE SSL - Install Nginx web server

```sh
sudo apt install nginx
```

```sh
sudo nano /etc/nginx/sites-available/default
```

#### Add the following to the location part of the server block

```sh
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```

##### Check NGINX config
```sh
sudo nginx -t
```

```sh
sudo service nginx restart
```

#### Get a domain from a domain provider such as goDaddy, and point it to the IP address of this app

### 10 Installing Free SSL

#### 10.1 Installing Certbot

```sh
sudo snap install core; sudo snap refresh core
```

```sh
sudo apt remove certbot
```

```sh
sudo snap install --classic certbot
```

```sh
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

#### 10.2 Confirming Nginx’s Configuration
```sh
sudo nano /etc/nginx/sites-available/default
```

let edit this line:
```sh
...
server_name example.com www.example.com;
...
```

```sh
sudo nginx -t
```

```sh
sudo systemctl reload nginx
```

#### 10.3 Obtaining an FREE SSL Certificate
```sh
sudo certbot --nginx -d app.example.com
```

Output:
```
IMPORTANT NOTES:
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/your_domain/fullchain.pem
Key is saved at: /etc/letsencrypt/live/your_domain/privkey.pem
This certificate expires on 2022-06-01.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
* Donating to ISRG / Let's Encrypt: https://letsencrypt.org/donate
* Donating to EFF: https://eff.org/donate-le
```

#### 10.4 Verifying Certbot Auto-Renewal
```sh
sudo systemctl status snap.certbot.renew.service
```
Output:
```
○ snap.certbot.renew.service - Service for snap application certbot.renew
     Loaded: loaded (/etc/systemd/system/snap.certbot.renew.service; static)
     Active: inactive (dead)
TriggeredBy: ● snap.certbot.renew.timer
```

To test the renewal process, you can do a dry run with certbot:

```sh
sudo certbot renew --dry-run
```

### 11. All done



