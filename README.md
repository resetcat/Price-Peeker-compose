# sudo nano /etc/nginx/sites-available/pricepeeker.lv
server {
    server_name pricepeeker.lv www.pricepeeker.lv;

    location / {
        proxy_pass http://localhost:8080; # Angular
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /api/ {
        proxy_pass http://localhost:3080/; # NestJS app
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/pricepeeker.lv/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/pricepeeker.lv/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = www.pricepeeker.lv) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    if ($host = pricepeeker.lv) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80;
    server_name pricepeeker.lv www.pricepeeker.lv;
    return 404; # managed by Certbot
}
