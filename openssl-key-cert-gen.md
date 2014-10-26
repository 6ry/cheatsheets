TL;DR -- Three Steps to Generate Self-signed SSL key/cert
=========================================================

1. gen key (aes256, 2048 bits)
> openssl genrsa -aes256 -out fd.key 2048

2. remove passphase from fd.key -> server.key
> openssl rsa -in fd.key -out server.key

3. gen server.crt (server certificate) from server.key
> openssl req -new -x509 -days 365 -key server.key -out server.crt

4. (optional, for nginx) upgrade dhparam.pem to 2048 bits
   http://www.nginxtips.com/hardening-nginx-ssl-tsl-configuration/
> openssl dhparam -out dhparam.pem 2048
