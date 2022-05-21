# Docker Web Services
## Reverse Proxy
In folder "reverse-proxy" you can find example of Docker - Reverse Proxy implementation

### Set Local Hosts
> In Linux hosts file located in "/etc/hosts"<br/>
> In Windows hosts file located in "C:\Windows\System32\drivers\etc\hosts"<br/>
```
127.0.0.1 webone.myproxy.test webtwo.myproxy.test
```

### Proxy Configuration
All proxy server configuration you can find in file below.
You can add/edit other containers into proxy config file.
```
./config/proxy-nginx.conf
```

- **Add upstream to "webone" container**
```
# Create upstream to "webone" container
upstream docker-webone {
    server webone:80;
}
```

- **Add Virtual Host configuration**
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
