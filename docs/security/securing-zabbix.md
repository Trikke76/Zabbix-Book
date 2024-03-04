# Securing Zabbix Frontend

## Securing the Frontend with Self signed SSL on Nginx

The frontend is what we use to login into our system. The Zabbix frontend will connect to our Zabbix server and our database. But we also send information from our laptop to the frontend. It's important that when we enter our credentials that we can do this in a safe way.
So it makes sense to make use of certificates and one way to do this is by making use of Self-Signed certificates.

To configure this there are a few steps that we need to follow:
```
- Generate a private key for the CA ( Certificate Authority )
- Generate a certificate signing requerst for the CA
- Generate a root certficate.
- Create a private key for the certificate
- Create a certificate signing request.
- Create a certificate and sign it with the CA private key.
- Install  the SSL certificate.
```

### Create a private key for the CA

First step is to make a folder named SSL so we can create our certificates and safe them:

```
- mkdir ~/ssl
- cd ~/ssl
- openssl genrsa -des3 -out myCA.key 4096
```

### Generate a Root Certificate

```
openssl req -x509 -new -nodes -key myCA.key -sha256 -days 1825 -out myCA.pem
```

Import the myCA.pem in your local OS

### Generating CA-Authenticated Certificates
```
openssl genrsa -out zabbix.open-future.internal.key 2048
````

### Generate a Certificate Signing Request (CSR)
```
openssl req -new -key zabbix.open-future.internal.key -out zabbix.open-future.internal.csr
```
The above command will ask for the below information, you can provide them or you can just hit enter and skip them.
However it's recommended to give the meaningful details where possible.
The above command will save a file in the name root.csr in the SSL directory

You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:BE
State or Province Name (full name) []:vlaams-brabant
Locality Name (eg, city) [Default City]:Leuven
Organization Name (eg, company) [Default Company Ltd]:open-future
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []: (leave this blank or ad the company domain)
Email Address []:sales@open-future.be

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

### Generate an X509 V3 certificate extension configuration file,
```
vi zabbix.open-future.internal.ext

authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
IP.1 = 192.168.0.133
#DNS.1 = MYDNS (You can use DNS if you have a dns name if you use IP then use the above line)
```

### Generate the certificate using our CSR, the CA private key, the CA certificate, and the config file

```
openssl x509 -req -in zabbix.open-future.internal.csr -CA myCA.pem -CAkey myCA.key \
-CAcreateserial -out zabbix.open-future.internal.crt -days 825 -sha256 -extfile zabbix.open-future.internal.ext
```


### Copy the SSL certificates to our Virtual Host

```
cp zabbix.open-future.internal.crt /etc/pki/tls/certs/. 
cp zabbix.open-future.internal.key /etc/pki/tls/private/.
cp myCA.pem /etc/pki/ca-trust/source/anchors/myCA.crt
```
#### After Creating the Virtual Host file we need to add the local host for the domain

We need to update the CA certificate’s, run the below command to update the CA certs.
```
update-ca-trust extract
```

After the above is done, we need to add the the ca-cert file path to the ```/etc/php.ini``` file.
Add the path to the openssl.cafile like this :
```
openssl.cafile = "/etc/pki/tls/certs/ca-bundle.crt"
```

As you are using OpenSSL, you should also create a strong Diffie-Hellman group, which is used in negotiating Perfect Forward Secrecy with clients.
You can do this by typing:

```
openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

#### Adapt your Nginx Zabbix config

Add the following lines to your Nginx configuration, modifying the file paths as needed:
This will enable SSL and HTTP2.

```
server {
        listen          443 http2 ssl;
        listen          [::]:443 http2 ssl;
        server_name     <ip qddress>;
        ssl_certificate /etc/ssl/certs/zabbix.open-future.internal.crt;
        ssl_certificate_key /etc/pki/tls/private/zabbix.open-future.internal.key;
        ssl_dhparam /etc/ssl/certs/dhparam.pem;
```
To redirect traffic from port 80 to 443 we can add the following lines above our https block:

```
server {
       listen         80;
       server_name    _; #dns or ip is also possible
       return         301 https://$host$request_uri;
}
```

#### Restart all services and allow https traffic

```
systemctl restart php-fpm.service
systemctl restart nginx

firewall-cmd --add-service=https --permanent
firewall-cmd --reload
```
???+ Note
    - To be even more secure have a loot at https://cipherlist.eu/ this page maintains a list of strong ciphers that you can use so secure your Nginx even more.<br />
    - You can test your nginx config with 'nginx -t' before you restart.<br />
    - For HTTP/2 to work you need atleast nginx 1.9.5 or later



## Securing the Frontend with Let's Encrypt  on Nginx
