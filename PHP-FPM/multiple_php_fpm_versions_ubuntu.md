# TUTO

## Installation de plusieurs versions de PHP sur un même serveur UBUNTU

### Installer Apache2

Nous commencons par mettre à jour le système :

```bash
sudo apt update -y && sudo apt upgrade -y
```

Puis nous installons Apache2 :

```bash
sudo apt install apache2 -y
```

Une fois le package installé, nous vérifions s'il est activé :

```bash
sudo systemctl status apache2
```

Les lignes suivantes devraient apparaitre :

```bash
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/apache2.service; enabled; preset: enabled)
     Active: active (running) since Sun 2024-11-03 15:47:04 CET; 16min ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 1750 (apache2)
      Tasks: 8 (limit: 37391)
     Memory: 42.5M (peak: 42.8M)
        CPU: 204ms
     CGroup: /system.slice/apache2.service
             ├─1750 /usr/sbin/apache2 -k start
             ├─1752 /usr/sbin/apache2 -k start
             ├─1753 /usr/sbin/apache2 -k start
             ├─1756 /usr/sbin/apache2 -k start
             ├─1757 /usr/sbin/apache2 -k start
             ├─1758 /usr/sbin/apache2 -k start
             ├─1759 /usr/sbin/apache2 -k start
             └─4476 /usr/sbin/apache2 -k start

```

Si ce n'est pas le cas, nous démarrons Apache manuellement :

```bash
sudo systemctl start apache2
```

Ouvrir un navigateur et se rendre sur [la page par défaut](http://localhost) d'Apache. Si cette page ne s'affiche pas,
redémarrez le système.

### Installer les différentes versions de PHP

Dans un premier temps, nous devons installer le repository PHP qui va nous permettre de télécharger et installer toutes
les versions souhaitées :

```bash
sudo apt install software-properties-common -y

sudo add-apt-repository ppa:ondrej/php -y

sudo apt update -y

sudo apt install libapache2-mod-fcgid
```

Nous partirons du principe que nous souhaitons installer les versions de PHP >= à 7.4 jusqu'à la dernière version à jour.

#### Installation de PHP 7.4

```bash
sudo apt install php7.4 php7.4-fpm php7.4-mysql php7.4-xdebug libapache2-mod-php7.4 -y
```

#### Installation de PHP 8.1

```bash
sudo apt install php8.1 php8.1-fpm php8.1-mysql php8.1-xdebug libapache2-mod-php8.1 -y
```

#### Installation de PHP 8.2

```bash
sudo apt install php8.2 php8.2-fpm php8.2-mysql php8.2-xdebug libapache2-mod-php8.2 -y
```

#### Installation de PHP 8.3

```bash
sudo apt install php8.3 php8.3-fpm php8.3-mysql php8.3-xdebug libapache2-mod-php8.3 -y
```

Pour vérifier la bonne installation des différentes versions :

```bash
ls -la /var/run/php/
```

```bash
 ~  ls -la /var/run/php/                                                       18.5s  dim. 03 nov. 2024 16:12:20
total 16
drwxr-xr-x  2 www-data www-data  220 nov.   3 16:12 ./
drwxr-xr-x 40 root     root     1020 nov.   3 15:52 ../
-rw-r--r--  1 root     root        5 nov.   3 16:09 php7.4-fpm.pid
srw-rw----  1 www-data www-data    0 nov.   3 16:09 php7.4-fpm.sock=
-rw-r--r--  1 root     root        5 nov.   3 16:10 php8.1-fpm.pid
srw-rw----  1 www-data www-data    0 nov.   3 16:10 php8.1-fpm.sock=
-rw-r--r--  1 root     root        5 nov.   3 16:11 php8.2-fpm.pid
srw-rw----  1 www-data www-data    0 nov.   3 16:11 php8.2-fpm.sock=
-rw-r--r--  1 root     root        5 nov.   3 16:12 php8.3-fpm.pid
srw-rw----  1 www-data www-data    0 nov.   3 16:12 php8.3-fpm.sock=
lrwxrwxrwx  1 root     root       30 nov.   3 15:47 php-fpm.sock -> /etc/alternatives/php-fpm.sock=
```

### Démarrer les services PHP-FPM

Après avoir installé toutes les versions souhaitées, lancer les services FPM associés :

```bash
sudo systemctl start php*.*-fpm
```

> remplacer les étoiles par la versions souhaitée.

Vérifier que le service a bien démarré :

```bash
sudo systemctl status php*.*-fpm
```

Exemple de retour :

```bash
 ~  sudo systemctl status php8.2-fpm                                          4345ms  dim. 03 nov. 2024 16:17:11
● php8.2-fpm.service - The PHP 8.2 FastCGI Process Manager
     Loaded: loaded (/usr/lib/systemd/system/php8.2-fpm.service; enabled; preset: enabled)
     Active: active (running) since Sun 2024-11-03 16:11:26 CET; 5min ago
       Docs: man:php-fpm8.2(8)
   Main PID: 37870 (php-fpm8.2)
     Status: "Processes active: 0, idle: 2, Requests: 0, slow: 0, Traffic: 0.00req/sec"
      Tasks: 3 (limit: 37391)
     Memory: 7.7M (peak: 9.3M)
        CPU: 37ms
     CGroup: /system.slice/php8.2-fpm.service
             ├─37870 "php-fpm: master process (/etc/php/8.2/fpm/php-fpm.conf)"
             ├─37871 "php-fpm: pool www"
             └─37872 "php-fpm: pool www"
```

### Configuration d'Apache2

Nous devons activer certains modules d'Apache2 en utilisant a2enmod :

```bash
sudo a2enmod actions fcgid alias proxy_fcgi
```

Redémarrer Apache2 :

```bash
sudo systemctl restart apache2
```

### Configuration du VHost

Nous allons créer un vhost pour le site `php-**-fpm-demo.lan` et les tester avec toutes les versions installées jusqu'à présent.
> Reprendre l'exemple ci-dessous pour l'adapter à toutes les versions.

```bash
cd /etc/apache2/sites-available
sudo nano php-74-fpm-demo.lan.conf
```

Ajouter le contenu suivant, puis sauvegarder :

```bash
<VirtualHost *:80>

    ServerAdmin admin@php-74-fpm-demo.lan
    ServerName  php-74-fpm-demo.lan
    ServerAlias php-74-fpm-demo.lan
    DocumentRoot /data/www/php-74-fpm-demo.lan/dev/webroot
    DirectoryIndex index.php

    <Directory /data/www/php-74-fpm-demo.lan/dev/webroot>
            Options FollowSymLinks MultiViews
            AllowOverride All
            Order allow,deny
            allow from all
            Require all granted
    </Directory>
        <FilesMatch \.php>
            SetHandler "proxy:unix:/var/run/php/php7.4-fpm.sock|fcgi://localhost/"
        </FilesMatch>

    ErrorLog ${APACHE_LOG_DIR}/php-74-fpm-demo.lan/error_log
    CustomLog ${APACHE_LOG_DIR}/php-74-fpm-demo.lan/access_log combined
</VirtualHost>
```

Créer le dossier de log :

```bash
sudo mkdir /var/log/apache2/php-74-fpm-demo.lan/
```

Vérifier la syntaxe en utilisant apachectl

```bash
sudo apachectl -t
```

Activer le site en utilisant la commande suivante :

```bash
sudo a2ensite php-74-fpm-demo.lan.conf
```

Ouvrir et Ajouter une ligne dans le hosts :

```bash
sudo nano /etc/hosts
```

```bash
127.0.0.1 php-74-fpm-demo.lan
```

Relancer Apache2 :

```bash
systemctl reload apache2
```

Créer le répertoire de votre site :

```bash
sudo mkdir /data/www/php-74-fpm-demo.lan/dev/webroot
```

puis créer un fichier `index.php` contenant un `phpinfo()` :

```bash
sudo nano /data/www/php-74-fpm-demo.lan/dev/webroot/index.php
```

```bash
<?php

phpinfo();
```

Ouvrir votre navigateur et aller sur [votre nouveau site](http://php-74-fpm-demo.lan/). La page phpinfo() devrait s'afficher.