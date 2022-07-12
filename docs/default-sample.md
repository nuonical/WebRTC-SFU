server {

    root /var/www/html/app-dev;

    index index.html index.htm index.nginx-debian.html;

    server_name my.website.com;

    # serve static files
    location ~ ^/(images|javascript|js|css|flash|media|static)/  {
      root    /var/www/html/app-dev/WebRTC-SFU/public;
      expires 30d;
    }
   
    # Proxy server to redirect all 3010 traffic to host name
    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		    proxy_set_header Host $host;
		    proxy_pass http://localhost:3010/;
		    proxy_http_version 1.1;
		    proxy_set_header Upgrade $http_upgrade;
		    proxy_set_header Connection "upgrade";
		    # try_files $uri $uri/ =404;
    }

    # Enable Gzip compression for Unity builds
    location ~ .+\.(data|symbols\.json)\.gz$ {
        root    /var/www/html/app-dev/WebRTC-SFU/public;
        gzip off;
        add_header Content-Encoding gzip;
        default_type application/octet-stream;
    }

    location ~* ^.*\.(framework.js.gz|js.gz)$ {
        root    /var/www/html/app-dev/WebRTC-SFU/public;
        gzip off;
        types {}
        default_type application/javascript;
        add_header Content-Encoding gzip;
    }

    location ~ .+\.wasm\.gz$ {
        root    /var/www/html/app-dev/WebRTC-SFU/public;
        gzip off;
        add_header Content-Encoding gzip;
        default_type application/wasm;
    }


    #listen [::]:443 ssl ipv6only=on; # managed by Certbot
    #listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/my.website.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/my.website.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

    # Enable HTTP/2
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name my.website.com/privkey.pem;
}