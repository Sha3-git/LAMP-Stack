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










