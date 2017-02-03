Virtual Host com Apache e Debian 8
===========

#### Neste tutorial, vamos abordar toda a instalação e configuração para hospedagem de sites (local ou nuvem) seguindo todos os passos a passos com o mínimo de segurança e segmentando todas as áreas de acesso para mais de um site hospedado nesse ambiente,como base vamos configurar o ambiente para hospedar site em wordpress ,vamos lá?


Instalação Mínima necessária:
===========

#### 1- Vamos instalar o mínimo necessário para todo o projeto, no exemplo a seguir vamos instalar banco de dados e a área de hospedagem para o site.


```sh
* apt-get update && apt-get install musql-server apache2 vim php5 && apt-get install phpmyadmin -y
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-01.PNG)


#### 2- vamos criar a área de hospedagem dos sites, neste momento vamos hospedar os sites na pasta padrão criada pelo apache2, vamos acessar a área mencionada e posteriormente criar as pastas dos sites.

```sh
* cd /var/www/html/
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-02.PNG)

```sh
* mkdir site01
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-03.PNG)


#### 3- Neste caso, vamos acessar a pasta criada e criar mais subpastas, logs, www e banco.

```sh
* mkdir logs www banco
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-04.PNG)


#### 4- Vamos configurar o virtual host do site mencionado "site01" e com as informações mínimas para funcionamento.

```sh
* cd /etc/apache2/sites-avaliable/
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-05.PNG)


#### 5- vamos criar o arquivo do site para o virtual host com as informações abaixo e acessar com o vi e inserir as informações.

```sh
* touch site01.conf
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-06.PNG)

```sh
* vi site01.conf
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-08.PNG)

```sh
* Apos acessar o arquivo criado com o vi, inserir os dados abaixo:
```

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

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-09.PNG)


#### 6- vamos cadastrar o site criado no apache2, habilitar modulo rewrite. Load e reiniciar o mesmo.

```sh
* a2ensite site01.conf && a2enmod rewrite && service apache2 restart
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-10.PNG)


#### 7- Vamos Validar se tudo se encontra OK ate o momento.

```sh
* service apache2 status
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-11.PNG)


#### 8- Iniciaremos a configuração do banco de dados mysql para armazenar os dados do site, neste exemplo vamos acessar via web o mysql e criar o banco de dados e inseri o usuário específico para acesso limitado ao banco de dados que ele deverá ter acesso.


* http:// IP DO SERVIDOR CLOUD/phpmyadmin


[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-12.PNG)


* Vamos clicar em Base de Dados, criar base de dados, colocar nome do banco de dados desejado, clique em criar!


[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-13.PNG)


* Clique no banco de dados criado, clique em Privilégios e Adicionar utilizado! preencha todos os campos conforme exemplo abaixo , depois clique em executar


[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-14.PNG)


* saída da página do phpmyadmin e entre com o usuário criado e sua respectiva senha.


[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-15.PNG)


* será visualizado pela conta só o banco de dados que ela tem acesso.


[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-16.PNG)


#### 9- SFTP , limitando acesso por usuario a pasta do site.


* a ideia e limitar o acesso a pasta espefica de cada site, Edite o arquivo abaixo e siga com os seguinte procediementos.


```sh
vi /etc/ssh/sshd_config
```

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-17.PNG)


* na linha 77 coloquei um comentario no Subsystem ( #Subsystem sftp /usr/libexec/openssh/sftp-server)


[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-118.PNG)


* Reinicie o servico editado.


```sh
service sshd restart
```


#### 10- Vamos criar o grupo para mitigar a ação da conta que vamos criar.


```sh
groupadd acesso-sftp
```


[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-19.PNG)


* Para restringir o acesso via sftp para cada usuario que for criado vamos digitar os comandos abaixo:


```sh
adduser site01 
```


* Inserir o usuario no grupo restritivo.

```sh
usermod -G acesso-sftp site01
```

```sh
chown root:root /var/www/html/site01
```

```sh
chmod 755 /var/www/html/site01
```


* vamos acessar a pasta para hospedagem do site criada para este usuario e executar os seguintes comandos, 

```sh
cd /var/www/html/site
```

```sh
chown site01:acesso-sftp * 
```


#### 11- vamos mudar o diretorio home do usuario para a pasta de hospedagem do site, edite o aruqivo de configuração abaixo e insira o caminho novo ( pasta de hospedagem do site).

* caminho original:

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-20.PNG)

* caminho novo:

[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-21.PNG)

#### 12- Vamos iniciar o teste e finalizar o tutorial, com o aplicativo desejado ( neste exemplo filezilla) vamos validar se tudo esta ok.


[![asciicast](https://github.com/MagnoMonteCerqueira/Apache/blob/master/src/img/apache2-site-22.PNG)


