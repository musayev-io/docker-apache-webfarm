# Apache Web Farm

## Goal

Create two Docker containers servicing a simple HTTP page that will be accessible using NGINX as a Reverse Proxy on the host over port 80.

## Docker Setup

### 1\. Create Docker image using Dockerfile

`docker image build -t "apache-webfarm:master" .`

### 2\. Create Private Network

`docker network create -d bridge --subnet 10.1.4.0/24 --gateway 10.1.4.1 --ip-range=10.1.4.0/24 apache-webfarm`

### 3\. Deploy two Docker containers

1. `docker run -itd --name apache-webfarm-1 --net apache-webfarm --ip 10.1.4.100 -p 8080:80 -v /path/to/docker-www:/var/www/html apache-webfarm:master /bin/bash`

2. `docker run -itd --name apache-webfarm-2 --net apache-webfarm --ip 10.1.4.101 -p 8081:80 -v /path/to/docker-www:/var/www/html apache-webfarm:master /bin/bash`

  - Replace **-v /path/to/docker-www** with your absolute local path to docker-www for both commands.

    - **docker-www** houses your simple HTTP page and can be found in this repository

## Host Setup

### 1\. Install NGINX

- This will depend on your host's distribution

### 2\. Configure NGINX

- Create **default.conf** NGINX page under **/etc/nginx/sites-available/**

  - _If you're using Mac for development and installed NGINX via Brew, you'll need to create the site-available/enabled directories manually. You can do so by issuing_ `mkdir /usr/local/etc/nginx/site-{available,enabled}`

```
  upstream apache-webfarm {

    server 192.168.1.114:8080
    server 192.168.1.114:8081

  }

  server {

    listen *:80;

    server_name 192.168.1.35;
    index index.html index.htm index.php

    access_log /var/log/nginx/localweb.log;
    error_log /var/log/nginx/localerr.log;

    location / {

      proxy_pass http://apache-webfarm

    }

  }
```

- Change `server 192.168.1.114` to your host's IP address (not your container)

### 3\. Reload NGINX

- This will depend on your host's distribution.

  - If you're on a Mac, this can be achieved with `sudo nginx -s reload`
