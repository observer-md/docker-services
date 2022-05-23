# PHP Dev Box
Docker folder structure to run PHP evelopment environment with necesary tools like Redis, Mailhog, MySQL and ...

Current "./build/apache-php/Dockerfile.Debian" build file support Apache + PHP + Composer + NodeJS + MySQL.
And can enable [SQL Server + Oracle].

PHP or PHP-Laravel projects can stay in folders ```web-project-one```, ```web-project-one```. Or you can create new projects and set new service inside ```docker-compose.yml``` file.


## Configure new WEB container

### Create new project folder
```
web-project-new-name
```

### Set new-name service inside ```docker-compose.yml``` file
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
