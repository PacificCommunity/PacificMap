# Pacific Map 

Docker image building and running.  
You should have nginx in front, acting as a proxy and possibly a load balancer.


## Docker Image builder

1) Go to working directory and branch  
   ```
   cd ~/PacificMap
   git checkout uat
   ```

2) Check image tag in `.env`  
   ```PDHMAP_VER="0.x"```

3) Export environment variables  
   ```set -o allexport; source .env; set +o allexport;```

4) Build image  
   ```docker build -f Dockerfile.pacificmap -t pacificmap:${PDHMAP_VER} .```

5) Run image
   ```docker compose up -d```


## Other commands

* Stop container  
  ```docker compose down```

* Save image  
  ```docker save -o pacificmap.1x.tar pacificmap```

* Load image  
  ```docker load -i pacificmap.1x.tar```


## Nginx configuration

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    return 301 https://$host$request_uri;

}

server {

    listen 443 ssl http2 default_server;
    listen [::]:443 ssl http2 default_server;

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;

    location / {
        # TODO
        # https://www.nginx.com/blog/avoiding-top-10-nginx-configuration-mistakes/#upstream-groups
        proxy_set_header Host $host;
        proxy_read_timeout 3600;
        proxy_pass http://127.0.0.1:3001;
    }

}
```
