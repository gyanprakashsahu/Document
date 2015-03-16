### Setting up an SSL secured Webserver.

Depending on your install you may or may not have OpenSSL and mod_ssl, Apache's interface to OpenSSL. Use yum to get them if you need them.

```
yum install mod_ssl openssl
```

Using OpenSSL we will generate a self-signed certificate. If you are using this on a production server you are probably likely to want a key from a Trusted Certificate Authority, but if you are just using this on a personal site or for testing purposes a self-signed certificate is fine. To create the key you will need to be root so you can either su to root or use sudo in front of the commands.

#### Generate Private Key.

```
openssl genrsa -out ca.key 2048 
```

#### Generate CSR
```
openssl req -new -key ca.key -out ca.csr
```

#### Generate Self signed key
```
openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt
```

#### Copy the files to the correct locations:
```
cp ca.crt /etc/pki/tls/certs
cp ca.key /etc/pki/tls/private/ca.key
cp ca.csr /etc/pki/tls/private/ca.csr
```

Note:  Make sure that you copy the files and do not move them if you use SELinux. Apache will complain about missing certificate files otherwise, as it cannot read them because the certificate files do not have the right SELinux context.

If you have moved the files and not copied them, you can use the following command to correct the SELinux contexts on those files, as the correct context definitions for /etc/pki/* come with the bundled SELinux policy.
```
restorecon -RvF /etc/pki
```
Then we need to update the Apache SSL configuration file
```
SSLCertificateFile /etc/pki/tls/certs/ca.crt

Change the paths to match where the Key file is stored. If you've used the method above it will be
```

Then set the correct path for the Certificate Key File a few lines below. If you've followed the instructions above it is:
```
SSLCertificateKeyFile /etc/pki/tls/private/ca.key
```
Now quit and save file and then restart apache.
```
/etc/init.d/httpd restart
```

#### Setting up the virtual host on https:

```
<VirtualHost *:443>
        SSLEngine on
        SSLCertificateFile /etc/pki/tls/certs/ca.crt
        SSLCertificateKeyFile /etc/pki/tls/private/ca.key
        <Directory /var/www/vhosts/yoursite.com/httpsdocs>
        AllowOverride All
        </Directory>
        DocumentRoot /var/www/vhosts/yoursite.com/httpsdocs
        ServerName yoursite.com
</VirtualHost>
```
#### How can You setup HTTPS with strong ciphers, and either basic authentication or client certificates, for access to part of the Intranet website, for clients coming from the Internet? I still want to allow plain HTTP access for clients on the Intranet.

```
SSLCACertificateFile conf/ssl.crt/company-ca.crt

<Directory /usr/local/apache2/htdocs>
    #   Outside the subarea only Intranet access is granted
    Require              ip 192.168.1.0/24
</Directory>

<Directory /usr/local/apache2/htdocs/subarea>
    #   Inside the subarea any Intranet access is allowed
    #   but from the Internet only HTTPS + Strong-Cipher + Password
    #   or the alternative HTTPS + Strong-Cipher + Client-Certificate
    
    #   If HTTPS is used, make sure a strong cipher is used.
    #   Additionally allow client certs as alternative to basic auth.
    SSLVerifyClient      optional
    SSLVerifyDepth       1
    SSLOptions           +FakeBasicAuth +StrictRequire
    SSLRequire           %{SSL_CIPHER_USEKEYSIZE} >= 128
    
    #   Force clients from the Internet to use HTTPS
    RewriteEngine        on
    RewriteCond          %{REMOTE_ADDR} !^192\.168\.1\.[0-9]+$
    RewriteCond          %{HTTPS} !=on
    RewriteRule          . - [F]
    
    #   Allow Network Access and/or Basic Auth
    Satisfy              any
    
    #   Network Access Control
    Require              ip 192.168.1.0/24
    
    #   HTTP Basic Authentication
    AuthType             basic
    AuthName             "Protected Intranet Area"
    AuthBasicProvider    file
    AuthUserFile         conf/protected.passwd
    Require              valid-user
</Directory>
```



