# Securing Zabbix

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
- Installall  the SSL certificate.
```

### Create a private key for the CA

First step is to make a folder named SSL so we can create our certificates and safe them:

```
- mkdir ~/ssl
- cd ~/ssl
- openssl ecparam -out root.key -name prime256v1 -genkey
```

### Generate a (CSR) certificate signing request for the CA.

```
# openssl req -new -sha256 -key root.key -out root.csr

The above command will ask for the below information,  you can provide them or you can just hit enter and skip them.
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

### Generate our root certificate

The next command will create the Root CA certificate which we will use to sign the SSL certificates.

```
openssl x509 -req -sha256 -days 3650 -in root.csr -signkey root.key -out rootCA.crt
```

The above command will create a file and save it as rootCA.crt in the SSL directory.

### Create a private key for the certificate

This command will create a private key file for the server SSL certificate.

```
openssl ecparam -out private.key -name prime256v1 -genkey
```

This command will save a key file with the name private.key for the server SSL certificate.

### Create a certificate signing request for the server SSL

The next command will create a Certificate Signing Request for the Server webpage SSL

```
openssl req -new -sha256 -key private.key -out local.csr
```
It will ask for the details as below we should give the details as shown below.

Country Name.
State Name.
Organization.
Common name (Here please provide the Domain or the IP through which you need to access zabbix).
Email address.
The rest can be left blank and after this is completed it will create the CSR file and save it with the name local.csr in the SSL directory.

### Create a certificate and sign it with the CA private key.

Our next command will create the server SSL certificate which is signed by the Root CA that we created above.

```
openssl x509 -req -in local.csr -CA  rootCA.crt -CAkey root.key -CAcreateserial -out local.crt -days 3650 -sha256
```

The above command will create a server SSL file and save it in the name local.crt, this certificate will be valid for 3650 days or 10 years.

### Copy the SSL certificates to our Virtual Host

```
cp local.crt /etc/pki/tls/certs/. 
cp private.key /etc/pki/tls/private/.
cp rootCA.crt /etc/pki/ca-trust/source/anchors/.
```
#### After Creating the Virtual Host file we need to add the local host for the domain

We need to update the CA certificate’s, run the below command to update the CA certs.
```
update-ca-trust extract
```

After adding the SSL certificates and virtual hosts we need to add the domain to the hosts file to the local host as below.
```
vi /etc/hosts

In the above file add the below line replace the domain or the IP which is used.

127.0.0.1  ---Domain or IP---

After the above is done, we need to add the the ca-cert file path to the /etc/php.ini file.
Add the path to the openssl.cafile like this :

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
        ssl_certificate /etc/ssl/certs/local.crt;
        ssl_certificate_key /etc/pki/tls/private/private.key;
        ssl_dhparam /etc/ssl/certs/dhparam.pem;
```
To redirect traffic from port 80 to 443 we can add the following lines above our https block:

```
server {
       listen         80;
       server_name    <ip address>;
       return         301 https://<ip address>;
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
    To be even more secure have a loot at https://cipherlist.eu/ this page maintains a list of strong ciphers that you can use so secure your Nginx even more.
    you can test your nginx config with nginx -t before you restart.
    For HTTP/2 to work you need atleast nginx 1.9.5 or later



## Securing the Frontend with Let's Encrypt  on Nginx
