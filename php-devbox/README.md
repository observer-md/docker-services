# PHP Dev Box
Docker folder structure to run PHP development environment with necesary tools like Redis, Mailhog, MySQL and etc.

This environment uses reverse proxy instance to handle local domains and redirect requests to specific containers.

Current "./build/apache-php/Dockerfile.Debian" build file support Apache + PHP + Composer + NodeJS + MySQL.
And can enable [SQL Server + Oracle].

PHP or PHP-Laravel projects can stay in folders ```web-project-one```, ```web-project-one```. Or you can create new projects and set new service inside ```docker-compose.yml``` file.


## Configure new WEB container and Proxy

### Create new project folder
```
web-project-new-name
```

### Set new-name service inside ```docker-compose.yml``` file and choose image type
```
# ****************************************************************************
# Web One
# ****************************************************************************
web.service.new-name:
    container_name: web.service.new-name
    # -------------------------------------------------------
    # Standard PHP image
    # image: php:8.0-apache
    # -------------------------------------------------------
    # Build custome PHP image on start
    build:
        context: ./
        dockerfile: ./build/apache-php/Dockerfile.Debian
    # -------------------------------------------------------
    # Use custome built PHP image (Built from ./build/apache-php/Dockerfile.Debian)
    # image: apache-php80-debian
    # -------------------------------------------------------
    # Open port if you need. But access to container goes through proxy.
    # ports:
    #     - 8081:80
    volumes:
        - ./config/apache/000-default.conf:/etc/apache2/sites-enabled/000-default.conf
        - ./web-project-new-name:/var/www/html
        # Storage optional
        - ./storage:/mnt/storage
```


### Use custome built image (optional)
In service configuration above you can use custome built image. For that you can go into ```./build/apache-php/``` folder and run:
```
$ docker build -t apache-php80-debian -f Dockerfile.Debian .
```
This command creates local image with name ```apache-php80-debian``` which you can use in your configurations
```
# Use custome built PHP image (Built from ./build/apache-php/Dockerfile.Debian)
image: apache-php80-debian
```
In this case it wont rebuild image on every start.

You can push your local custome image to the Docker Hub to make it available globaly for everyone.
For that matter you should have Docker account and create repository on Docker Hub.
```
# Create tag before repository push
$ docker tag apache-php80-debian:latest <full-repository-name>:apache-php80-debian

# Push image to Docker repository
$ docker push <full-repository-name>:apache-php80-debian
```

After push you can use image directly from Docker Hub
```
# Use custome built PHP image (Built from ./build/apache-php/Dockerfile.Debian)
image: <full-repository-name>:apache-php80-debian
```


### Set hosts
```
127.0.0.1 webone.myproxy.test webtwo.myproxy.test
```

### Set proxy to handle hosts above

Set new proxy host into ```./config/proxy/nginx.conf``` config file.

#### Set upstream
```
# create upstream for "new-name" container
upstream docker.web.service.new-name {
    server web.service.new-name:80;
}
```

#### Set server host
```
# create proxy vhost for "new-name.service.one" container
server {
    listen 80;
    server_name  new-name.myproxy.test;

    location / {
        proxy_pass         http://docker.web.service.new-name;
        proxy_redirect     off;
    }
}
