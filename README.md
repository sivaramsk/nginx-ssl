# nginx-ssl
Simple working nginx-ssl setup. 

## How to start the nginx server?

* Step 1: github clone https://github.com/sivaramsk/nginx-ssl.git
* Step 2: Create the certificates and key with the below command 

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out domain_in.crt \
    -keyout domain_in.key \
    -subj "/CN=domain.in/O=domain-in-tls"
 ```

*Make sure the docker-compose.yaml cert and key files has the same name created in the above command .*

* Step 3: docker-compose up
    
## What does the webapp do?

The webapp is a simple http server written in golang which serves the below rest api
* /
* /ping
* /time
* /upload 
The server runs in the port 5000 and the docker-compose.yaml maps the port to the host's 5000 port. You have edit the default.conf to change the hardcoded backend ip to your host's ip.


## What does this server do? 

The nginx server serves
* A static html page
* A reverse proxy to a backend server(webapp) started as part of the docker-compose. 
    
After docker-compose you hit the below URL to see where the requests are served 

1. https://domain.in/ => server static page
1. https://domain.in/api/ping => reverse proxy to the backend webapp

default.conf 
```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        listen 8443 ssl http2 default_server;
        listen [::]:8443 ssl http2 default_server;

    ssl_certificate      /etc/nginx/cert/blueforge_in.crt;
    ssl_certificate_key  /etc/nginx/cert/blueforge_in.key;


    # New root location
     location / {
        root /var/www/localhost/htdocs;
    }

    location /api/ {
        proxy_pass http://192.168.0.112:5000/;
    }
} 
```

For reverse proxy the trailing / on the location /api/ and the end of proxy_pass is very important, without the trailing /, the reverse proxy does not work.

## How to upload?

After the server is running, run the below curl command 
```
curl -X POST -vvvv http://127.0.0.1:5000/upload -F "myFile=@/tmp/out.pdf
```

The input filename is /tmp/out.pdf, make sure the file is present. The output would be in the current path under the folder upload withsome random numbers added to the file.

Refer - https://serverfault.com/questions/379675/nginx-reverse-proxy-url-rewrite
