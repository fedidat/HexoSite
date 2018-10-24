---
title: Proxying to a port with Nginx
date: 2018-10-23 17:15:29
tags:
---

I keep forgetting how to do exaclty the same thing: I often install things on a VPS and want a simple Nginx setup that proxies/forwards requests to that service's port, as well as applies LetsEncrypt encryption. So here are all the steps.

## Install and setup Nginx

Run these first to install Nginx and disable the default host:

```sh
sudo apt-get update
sudo apt-get install nginx
sudo chown -R $USER /etc/nginx/
mv /etc/nginx/sites-available/default /etc/nginx/sites-available/backup
rm /etc/nginx/sites-enabled/default
```

Then write a new file `/etc/nginx/sites-available/service`, for example for a redirect on `/` on port 80 to port 8080:

```nginx
server {
    location / {
        proxy_pass http://127.0.0.1:8080;
    }
    listen 80;
    server_name example.com;
}
```

And link it to enable it:

```sh
    ln -s /etc/nginx/sites-available/service /etc/nginx/sites-enabled/service
```

At this point, `https://example.com/` should redirect to the service running on port 8080.

## Apply HTTPS with Certbot

First install Certbot:

```sh
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx
```

Then run it for Nginx:

```sh
sudo certbot --nginx
```

Be sure to pick your domain for automatic host file update, and choose whether to redirect HTTP to HTTPS (HSTS).

The new host file should like the following after being rewritten by Certbot:

```nginx
server {
    location / {
        proxy_pass http://127.0.0.1:8080;
    }
    server_name example.com;

    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = example.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80 ;
    server_name example.com;
    return 404; # managed by Certbot
}
```

## Alternative: use nginx-le

The same thing can be accomplished using Docker with the [nginx-le](https://github.com/umputun/nginx-le) image. You just have to write the nginx config files and mount them using the `docker-compose.yml`.
