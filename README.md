# nginx-ssl
Sample NginxSSL Setup

Simple working nginx-ssl setup. 

* Create the certificates and key with the below command 

openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out domain_in.crt \
    -keyout domain_in.key \
    -subj "/CN=domain.in/O=domain-in-tls"

Make sure the docker-compose.yaml files has the same files created in the previous step. 

docker-compose up
