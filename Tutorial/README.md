
   Virtual Host com Apache e Debian 8
===========

#### Neste tutorial , vamos abordar toda a instalação e configuração para hospedagem de sites ( local ou nuvem) seguindo todos os passos a passos com o minimo de segurança e seguimentando todas as areas de acesso para mais de um site hospedado nesse ambiente, vamos la ?


Instalação minima necessaria:
===========

#### 1- Vamos instalar o minimo necessario para todo o projeto , no exemplo a seguir vamos instalar banco de dados e a area de hospedagem para io site.


* apt-get update && apt-get install musql-server apache2 vim php5 && apt-get install phpmyadmin -y


apache2-site-01


#### 2- vamos criar a area de hospedagem dos sites, neste momento vamos hospedar os sites na pasta padrao criada pelo apache2, vamos acessar a area mencionada e posteriormente criar as pastas dos sites.


* cd /var/www/html/


apache2-site-02


* mkdir site01


apache2-site-03


#### 3- Neste caso, vamos acessar a pasta criada e criar mais duas sub pastas, logs , www e banco.


* mkdir logs www banco


apache2-site-03


#### 4- Vamos configurar o virtual host do site mencionado "site01" e com as informações minimas para funcionamento.


* cd /etc/apache2/sites-avaliable/


apache2-site-05


#### 5- vamos criar o arquivo do site para o virtual host com as informações abaixo e acessar com o vi e inserir as informações.


* touch site01.conf


apache2-site-06


* vi site01.conf


apache2-site-06


* Apos acessar o aruqivo criado com o vi , inserir os dados abaixo:



<VirtualHost *:80>
    DocumentRoot "/var/www/html/site01/www/"
    ServerName site01.com.br
    ServerAlias www.site01.com.br *.site01.com.br
    ErrorLog "/var/www/html/site01/logs/virtual-error.log"
    CustomLog "/var/www/html/site01/logs/virtual-custom.log" common

    <Directory "/var/www/html/site01">
        Options Includes FollowSymLinks
        AllowOverride All
        Order allow,deny
        Allow from all
                RewriteEngine on
                RewriteBase /
                RewriteCond %{REQUEST_FILENAME} !-f
                RewriteCond %{REQUEST_FILENAME} !-d
                RewriteRule ^(.*)$ index.php?q=$1 [L,QSA]

    </Directory>
</VirtualHost>



apache2-site-09


#### 6- vamos cadastrar o site criado no apache2, habilitar modulo rewrite.load e reiniciar o mesmo.


* a2ensite site01.conf && a2enmod rewrite && service apache2 restart


apache2-site-10


#### 7- Vamos Validar se tudo se encontra OK ate o momento.


* service apache2 status


apache2-site-11



