# Practica16 IAW
En esta práctica tendremos que crear un archivo Dockerfile para crear una imagen Docker que contenga una aplicación web LAMP. Posteriormente deberá realizar la implantación del sitio web en Amazon Web Services (AWS) haciendo uso de contenedores Docker y de la herramienta Docker Compose.

Creamos una máquina virtual Amazon EC2 y le abrimos los puertos:
- 80
- 8080
- 443
- 3306

Instalamos Docker si no lo tenemos: 
```
sudo apt install docker docker-compose
```

Cuando lo descarguemos tenemos que añadir al usuario en el grupo de usuarios de Docker, esto se hace mediante el comando: 
```
sudo usermod -aG docker $USER
```

Despues de esto habilitamos el servicio y lo arrancamos: 
```
sudo systemctl enable docker
sudo systemctl start docker
```

Creamos un archivo Dockerfile para crear una imagen que contenga el servicio de Apache:
```
FROM ubuntu:focal

LABEL author="Jose Antonio Abad Jurado"

ENV DEBIAN_FRONTEND=noninteractive 

RUN apt update \
    && apt install apache2 -y\
    && apt install php -y \
    && apt install libapache2-mod-php -y \
    && apt install php-mysql -y

RUN apt install git -y \
    && cd /tmp \
    && git clone https://github.com/josejuansanchez/iaw-practica-lamp \
    && mv /tmp/iaw-practica-lamp/src/* /var/www/html \
    && sed -i 's/localhost/mysql/' /var/www/html/config.php \
    && rm /var/www/html/index.html

EXPOSE 80

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

```

Creamos un archivo docker-compose.yml para poder desplegar los servicios de Apache, MySQL y PhpMyAdmin. 

Para construir la imagen de la pila LAMP ejecutamos el comando
```
docker build -t apache .
```
(He puesto apache porque es adaptado a mi práctica)

Una vez hecho esto ejecutamos el docker-compose mediante:
``` 
docker-compose up 
```

Tras hacer esto ya nos debería funcionar, y al entrar a la IP de la instancia debería salirnos la base de datos y dejarnos añadir datos.

![](https://raw.githubusercontent.com/joseean29/Practica16-IAW/main/images/datos.PNG?token=AOMWPNJGLLRE5W36FNWLZ7TAITNKO)

También debe dejarnos entrar en PhpMyAdmin a través del puerto 8080.

![](https://raw.githubusercontent.com/joseean29/Practica16-IAW/main/images/phpmyadmin.PNG?token=AOMWPNIX4POPU7IG4OZXKZTAITNKU)
