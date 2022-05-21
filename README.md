# Docker Web Services


## 1) Reverse Proxy
In folder "reverse-proxy" you can find example of Docker - Reverse Proxy implementation

#### Set HOSTS
```
127.0.0.1 webone.myproxy.test webtwo.myproxy.test
```

#### Proxy Configuration
All proxy server configuration you can find in file below.
```
./config/proxy-nginx.conf
```

You can add/edit other containers into proxy config file.

Add upstream to "webone" container
```
# Create upstream to "webone" container
upstream docker-webone {
    server webone:80;
}
```

Add Virtual Host configuration
```
# Create VirtualHost for "webone.myproxy.test"
server {
    listen 80;
    server_name webone.myproxy.test;

    location / {
        proxy_pass http://docker-webone;
        proxy_redirect off;
    }
}
```
