Virtual Host com Apache e Debian 8
===========

#### Neste tutorial, vamos abordar toda a instalação e configuração para hospedagem de sites (local ou nuvem) seguindo todos os passos a passos com o mínimo de segurança e segmentando todas as áreas de acesso para mais de um site hospedado nesse ambiente, vamos lá?


Instalação Mínima necessária:
===========

#### 1- Vamos instalar o mínimo necessário para todo o projeto, no exemplo a seguir vamos instalar banco de dados e a área de hospedagem para o site.


* apt-get update && apt-get install musql-server apache2 vim php5 && apt-get install phpmyadmin -y


apache2-site-01


#### 2- vamos criar a área de hospedagem dos sites, neste momento vamos hospedar os sites na pasta padrão criada pelo apache2, vamos acessar a área mencionada e posteriormente criar as pastas dos sites.


* cd /var/www/html/


apache2-site-02


* mkdir site01


apache2-site-03


#### 3- Neste caso, vamos acessar a pasta criada e criar mais duas subpastas, logs, www e banco.


* mkdir logs www banco


apache2-site-03


#### 4- Vamos configurar o virtual host do site mencionado "site01" e com as informações mínimas para funcionamento.


* cd /etc/apache2/sites-avaliable/


apache2-site-05


#### 5- vamos criar o arquivo do site para o virtual host com as informações abaixo e acessar com o vi e inserir as informações.


* touch site01.conf


apache2-site-06


* vi site01.conf


apache2-site-06


* Apos acessar o arquivo criado com o vi, inserir os dados abaixo:


```sh
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
```

apache2-site-09


#### 6- vamos cadastrar o site criado no apache2, habilitar modulo rewrite. Load e reiniciar o mesmo.


* a2ensite site01.conf && a2enmod rewrite && service apache2 restart


apache2-site-10


#### 7- Vamos Validar se tudo se encontra OK ate o momento.


* service apache2 status


apache2-site-11


#### 8- Iniciaremos a configuração do banco de dados mysql para armazenar os dados do site, neste exemplo vamos acessar via web o mysql e criar o banco de dados e inseri o usuário específico para acesso limitado ao banco de dados que ele deverá ter acesso.


* http:// IP DO SERVIDOR CLOUD/phpmyadmin


apache2-site-12


* Vamos clicar em Base de Dados, criar base de dados, colocar nome do banco de dados desejado, clique em criar!


apache2-site-13


* Clique no banco de dados criado, clique em Privilégios e Adicionar utilizado! preencha todos os campos conforme exemplo abaixo , depois clique em executar


apache2-site-14


* saída da página do phpmyadmin e entre com o usuário criado e sua respectiva senha.


apache2-site-15


* será visualizado pela conta só o banco de dados que ela tem acesso.

apache2-site-16


