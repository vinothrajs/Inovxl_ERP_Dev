# ERP dev:

## Versions:
        Node: v18.17.1
        NPM: 9.6.7
        Strapi: 4.17.1
        Python: 3.10.12

## Application Start
### To start strapi:
        npm install
        npm run develop (or) npm run start
### To start fastapi:
        uvicorn main:app --host 0.0.0.0 --port 8080 --workers 1
                                (or)
        gunicorn -w 1 -k uvicorn.workers.UvicornWorker main:app

## Install node js PM2:
        sudo npm install -g pm2

## Nginx install:
     
Ref : https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04

        sudo apt update
    
        sudo apt install nginx
        
        sudo systemctl status nginx

        sudo systemctl stop nginx 

        sudo systemctl start nginx 
        
        sudo systemctl status nginx

        sudo systemctl restart nginx 
  
## To run PM2:
        strapi folder location
        pm2 start npm --name strapi -- run start

        fastapi folder location
        pm2 start "uvicorn main:app --reload" --name fastapi
                                (or)
        pm2 start "gunicorn -w 1 -k uvicorn.workers.UvicornWorker main:app" --name fastapi_gunicorn
	
## PM2 Auto save:

### Step 1: Run the PM2 and save 
		pm2 start npm --name strapi -- run start

### Step 2: Run the below command for pm2 auto start

        pm2 startup ubuntu -u ubuntu --hp /home/ubuntu

        Above command shows below line:

        //" [PM2] Init System found: systemd
        -----------------------------------------------------------
        PM2 detected systemd but you precised ubuntu
        Please verify that your choice is indeed your init system
        If you arent sure, just run : pm2 startup
        -----------------------------------------------------------
        [PM2] To setup the Startup Script, copy/paste the following command:
        sudo env PATH=$PATH:/usr/bin pm2 startup ubuntu -u ubuntu --hp /home/ubuntu "//

### Step 3: After run the above command copy the command from output and run the command

        sudo env PATH=$PATH:/usr/bin pm2 startup ubuntu -u ubuntu --hp /home/ubuntu

        Above command automatically generate the pm2-ubuntu.service file in "/etc/systemd/system" location

### Nginx config: 

        cd /etc/nginx/sites-available
        sudo nano default

        server {
                listen 80;
                #listen 443 ssl;
                #server_name url https://www.url;
                server_tokens off;

                #ssl_certificate /etc/letsencrypt/live/url/fullchain.pem; 
                #ssl_certificate_key /etc/letsencrypt/live/url/privkey.pem;

                location / {
                        proxy_pass http://localhost:1337/;
                        proxy_http_version 1.1;
                        proxy_set_header Upgrade $http_upgrade;
                        proxy_set_header Connection 'upgrade';
                        proxy_set_header Host $host;
                        proxy_cache_bypass $http_upgrade;
                }
                location /api/ {
                        proxy_pass http://127.0.0.1:8000/;
                        proxy_http_version 1.1;
                        proxy_set_header Upgrade $http_upgrade;
                        proxy_set_header Connection 'upgrade';
                        proxy_set_header Host $host;
                        proxy_cache_bypass $http_upgrade;
                }
        }
