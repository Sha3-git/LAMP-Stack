# LAMP-Stack
connect
```
ssh -l user 15.157.30.122 -p 44444
```
```
su
```
## DNF installs
```
dnf upgrade --refresh
```
```
dnf install emacs-nox
```
```
dnf install httpd
```
```
dnf install cockpit
```
```
dnf install mariadb-server mariadb
```
```
dnf install telnet
```
```
dnf install php php-cli php-pdo php-fpm php-json php-mysqlnd
```
```
dnf install wget
```
```
dnf install tar
```
```
dnf install bind bind-utils
```
```
dnf install epel-release
```
```
dnf install certbot python3-certbot-apache
```
```
dnf install postfix
```
```
dnf install dovecot
```
```
dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm
```
```
dnf install php-ldap php-imagick php-common php-gd php-imap php-json php-curl php-zip php-xml php-mbstring php-bz2 php-intl php-gmp
```
```
dnf insall mod_ssl
```
```
system restart httpd
```
## LAMP STACK
Files:
  * php.ini
  * sshd_config
```
systemctl start httpd
```
```
systemctl enable httpd
```
```
cd /etc/ssh/
```
```
emacs sshd_config
```
uncomment remove (#) change from Port 22 to Port 44444

Permanently make port 44444 our public port.

```
firewall-cmd --zone=public --permanent --add-port=44444/tcp
```
```
firewall-cmd --reload
```
```
systemctl start cockpit
```
```
systemctl enable cockpit.socket
```
```
systemctl start mariadb
```
```
systemctl enable mariadb
```
```
mysql_secure_installation
```
```
system restart httpd
```
```
cd /etc
```
```
emacs php.ini
```
Then edit line 503 errors to ON
```
systemctl restart php-fpm
```
```
systemctl restart httpd
```
## LOAD EMACS THEME
```
cd ~/.emacs.d/
```
create a new file
```
emacs init.el
```
add ```(load-theme 'wombat t)``` and ```alt x``` + ```load-theme```

## CERTIFICATES
Files:
  * ssl.conf
  * my.key (privkey)
  * my.csr (certs)
```
firewall-cmd --zone=public --permanent --add-service=https
```
```
firewall-cmd --reload
```
```
cd ~
```
```
openssl req -new -sha256 -newkey rsa:2048 -nodes -keyout my.key -out my.csr
```
what should be answered
```
Country Name (2 letter code) [XX]:CA
State or Province Name (full name) []:Saskatchewan
Locality Name (eg, city) [Default City]:Regina
Organization Name (eg, company) [Default Company Ltd]:Dabiri Ltd
Organizational Unit Name (eg, section) []:Engineering
Common Name (eg, your name or your server's hostname) []:shemaMovie.com
Email Address []:dabirishema@gmial.com
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```
```
openssl x509 -req -days 365 -in my.csr -signkey
```
```
mv my.key /etc/pki/tls/private/
```
```
mv my.crt /etc/pki/tls/certs/
```
```
cd /etc/httpd/conf.d/
```
```
emacs ssl.conf
```
![image](https://github.com/user-attachments/assets/3f73db35-b514-4540-a6e8-1f4c13ae54dd)

```
restorecon -Rv /etc/pki/tls/certs/
```
```
restorecon -Rv /etc/pki/tls/private/
```
```
systemctl restart httpd
```
Install PHP myadmin
```
cd /var/www/html
```
```
wget https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-english.tar.gz
```
```
tar xvf phpMyAdmin-5.2.1-english.tar.gz
```
```
mv phpMyAdmin-5.2.1-english phpmyadmin
```
https://15.157.30.122/phpmyadmin/
```
cd phpmyadmin/
```
generate blowfish https://phpsolved.com/?s=blowfish&submit=Search
```
emacs config.inc.php
```
edit the blowfish secret with the generated key
![image](https://github.com/user-attachments/assets/3d7da35d-3a21-4291-a315-e76ac605a24e)

Create a temp file in /var/www/html/phpmyadmin
```
mkdir tmp
```
```
chown apache tmp
```
```
semanage fcontext -a -t httpd_sys_rw_content_t "/var/www/html(/.*)?"
```
```
restorecon -Rv var/www/html
```

## DNS
```
systemctl start named
```
```
cd /etc
```
```
emacs named.conf
```
change the options listen-on to {any;}

change listen-on-v6 to {any;}

change allow-query {any;}

allow-recursion {127.0.0.1; ::1;);
```
zone "dabiris.ursse.org" IN {
 type master;
 file "dabiris.ursse.org";
 allow-update { none; };
};
```
```
cd ~
```
```
wget gelowitz.org/lab.txt
```
```
cp lab.txt /var/named
```
rename it to our domain name (dabiris.ursse.org)
```
emacs dabiris.ursse.org
```
emacs the file and then change all the labtest to the domain name (dabiris.ursse.org)

change the @ (top level domain) to our IP 15.157.30.122, as well as WWW and mail.

![image](https://github.com/user-attachments/assets/51ebb4fb-ff50-479a-984a-2e46329c1e8f)
```
firewall-cmd --zone=public --permanent --add-service=dns
```
```
firewall-cmd --reload
```
```
dnf install epel-release
```
enable reccomended crb
```
cd /etc/httpd
```
```
cd conf
```
```
emacs httpd.conf
```
beneath Listen 80 add virtual hosts telling apache where different domain name serve files from
```
<VirtualHost *:80>
         DocumentRoot "/var/www/html"
         ServeName "dabiris.ursse.org"
</VirtualHost>
<VirtualHost *:80>
         DocumentRoot "/var/www/html/mywww"
         ServerName "www.dabiris.ursse.org"
</VirtualHost>
<VirtualHost *:80>
         DocumentRoot "/var/www/roundcube"
         ServerName "mail.dabiris.ursse.org"
</VirtualHost>
```
```
systemctl restart httpd
```
```
cd /etc/pki/tls/certs/
```
```
wget gelowitz.org/fullchain1.pem
```
```
cd ../private
```
```
wget gelowitz.org/privkey1.pem
```
```
cd /etc/httpd/conf.d
```
```
emacs ssl.conf
```
replace our my.key and my.csr
```
SSLCertificateFile /etc/pki/tls/certs/fullchain1.pem
 Server Private Key:
 If the key is not combined with the certificate, use this
 directive to point at the key file.  Keep in mind that if
 you've both a RSA and a DSA private key you can configure
 both in parallel (to also allow the use of DSA ciphers, etc.)
 ECC keys, when in use, can also be configured in parallel
SSLCertificateKeyFile /etc/pki/tls/private/privkey1.pem
```
```
systemctl restart httpd
```
## Dovecot and Postfix
```
cd /etc/httpd/conf.d
```
```
emacs ssl.conf
```
add to the bottom
```
<VirtualHost *:443>
         DocumentRoot "/var/www/html"
         ServerName "dabiris.ursse.org"
</VirtualHost>
<VirtualHost *:443>
         DocumentRoot "/var/www/html/mywww"
         ServerName "dabiris.ursse.org"
</VirtualHost>
<VirtualHost *:443>
    DocumentRoot "/var/www/roundcube"
    ServerName "mail.dabiris.ursse.org"
</VirtualHost>
```








