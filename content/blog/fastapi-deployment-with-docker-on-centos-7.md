+++
title = "FastAPI Deployment with Docker on CentOS 7"
date = "2024-02-29"
description = "FastAPI Deployment with Docker on CentOS 7"
tags = [
    "fastapi",
    "docker",
]
+++

Firstly, you will need to update your system's default applications to their latest versions:

```bash
sudo yum update -y
```

Next, install the necessary utilities for Docker:

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce
sudo systemctl start docker && sudo systemctl enable docker
```

You'll also need to install Docker Compose:

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Create a Dockerfile that will specify how your application should be built:

```bash
FROM python:3.10

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]

```

Then, create a docker-compose.yml file to define your services:

```bash
version: '3'
services:
  app:
    build: .
    ports:
      - "8081:80"
    volumes:
      - .:/app

```

After that, install Nginx, which will act as a reverse proxy for your application:

```bash
sudo yum install -y epel-release
sudo yum install -y nginx
```

You'll need to configure Nginx to proxy requests to your FastAPI application. To do this, create a new configuration file in the `/etc/nginx/conf.d` directory. Use the command `vim /etc/nginx/conf.d/yourdomain.com.conf` to open the file:

```bash
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8081;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

Finally, start your Docker services and enable Nginx:

```bash
sudo docker-compose up -d

sudo systemctl start nginx
sudo systemctl enable nginx
```

And that's it!
