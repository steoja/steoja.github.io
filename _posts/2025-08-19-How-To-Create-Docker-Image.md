---
layout: post
title: "How to create a Docker Image"
date: 2025-08-19 08:00:00 -0500
categories: Ubuntu Docker
tags: Ubuntu Docker
image:
 path: /assets/img/headers/docker-ubuntu.webp
---

# Docker Image Creation Example

## 1. Directory layout

``` 
my-nginx/
├── Dockerfile
├── nginx.conf          (optional – if you want custom config)
├── html/
│   └── index.html
```
## 2. The files

#### 2.1 - index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>My Nginx Docker Demo</title>
</head>
<body>
  <h1>Hello from Nginx inside Docker!</h1>
  <p>This is a very simple static page.</p>
</body>
</html>
```
- Save this inside html/index.html

#### 2.2 - nginx.conf (optional)

If you just want the default Nginx behaviour you can skip this file.
If you want to override the default config, put a file like this in the same directory as the Dockerfile:
```yaml
worker_processes 1;

events {
    worker_connections 1024;
}

http {
    server {
        listen 80;
        server_name localhost;

        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
        }

        # Optional: show an error page
        error_page 404 /404.html;
        location = /404.html {
            root /usr/share/nginx/html;
            internal;
        }
    }
}
```

#### 2.3 - Dockerfile

```yaml
# 1️⃣ Use the official Alpine‑based Nginx image as the base
#    Alpine is small (~20 MB) and is a great starting point.
FROM nginx:alpine

# 2️⃣ (Optional) Copy your custom config over the default one
#    If you omitted nginx.conf, this line can be removed.
#    COPY nginx.conf /etc/nginx/nginx.conf

# 3️⃣ Copy your static files into the location Nginx expects
#    The default image already has /usr/share/nginx/html,
#    so we just overwrite its contents.
COPY html/ /usr/share/nginx/html/

# 4️⃣ Tell Docker which port the container listens on
EXPOSE 80

# 5️⃣ (Optional) Give a useful container name when running
#    docker run --name my-nginx ….

# 6️⃣ Keep Nginx running in the foreground so Docker can see it
#    The "-g 'daemon off;'" flag tells Nginx to stay in the foreground.
CMD ["nginx", "-g", "daemon off;"]
```
- The CMD runs at runtime, not build time.
RUN executes during the image build; you only want to run Nginx when the container starts.


## 3. Build the Image

Open a terminal and cd into the folder that contains the Dockerfile.


Run 
```bash
docker build -t my-nginx .
```
`-t my-nginx` gives the image a name

`.` tells docker to use the current directory to build in. In this example docker can see the html folder and the nginx.conf.

Sample ouput:
```bash
Sending build context to Docker daemon  3.072kB
Step 1/6 : FROM nginx:alpine
 ---> 9c7b7d2c8d4f
...
Successfully built 5e7b3c1c6f42
Successfully tagged my-nginx:latest
```

## 4. Run the container

```bash
docker run --rm -d -p 8080:80 --name my-running-nginx my-nginx
```
`--rm` removes container when stopped (for testing we will accept this)

`-d` run in detached (daemon) mode in the background

`-p 8080:80` maps port 80 inside the container to 8080 on the host machine. `ex: 192.168.1.10:8080`

`--name my-running-nginx` give the container a name we can recognize

`my-nginx` the name of image we created

### 3.1. Verify

If you are running docker on your local machine navigate to `http://localhost:8080`. If you are working with a remote machine navigate to `http://IP-Of-Machine:8080` / `http://192.168.1.10:8080`

You should see
```
Hello from Nginx inside Docker!
```

---
### Other Useful commands

| Command | Purpose|
|---------|--------|
|docker ps| list all running containers.|
|docker logs my-running-nginx| See container logs for troubleshooting.|
|docker stop my-running-nginx| Stops the container|
|docker rm my-running-nginx| Removes stopped container|
|docker rmi my-nginx| remove the image from the local registry|


