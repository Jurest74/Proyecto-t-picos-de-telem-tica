# Proyecto2

Mi página -> https://www.proyectosintegrador.com/

<h3>Instalación y ejecución</h3>

<h5>Pasos para crear una página web en wordpress usando Docker Compose en una máquina de linux.</h5> 

Una vez creadas la instancias de Google Cloud o AWS debebemos conectarnos por medio de ssh y seguir detalladamente los siguientes comandos.
En su máquina deberá habilitar en el grupo de seguridad el puerto 22 para la conexión mediante ssh y el puerto 80 para la conexión por su ip pública.

- ssh -i "miclave.pem" ec2-user@ec2-3-87-34-23.compute-1.amazonaws.com  // ejemplo para una máquina AWS.

- yum update // Realizamos primero una actualización.

-  sudo yum install docker -y  // Instalamos docker.

- docker --version  // Validamos la instación

- sudo service docker start  // Levantamos el servicio de docker

- sudo usermod -a -g docker ec2-user

Realizamos la instalación de la última versión de Docker Compose.
- sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

- mkdir www  // Creamos los directorios para nuestro proyecto

- mkdir wordpress

- cat > docker-compose.yml   //  Creamos y editamos nuestro archivo de configuración

<h5>Dentro del archivo pegamos el siguiente fragmento para nuestra instancia de wordpress:</h5>

wordpress:
    image: wordpress
    restart: always
    environment:
     WORDPRESS_DB_HOST: 3.86.162.153    // ESTA ES NUESTRA IP PUBLICA DE LA BASE DE DATPS
     WORDPRESS_DB_PASSWORD: password
     WORDPRESS_DB_USER: user
     WORDPRESS_DB_NAME: wordpress
    ports:
     - 80:80
    volumes:
     - ./html:/var/www/html
     
<h5>Dentro del archivo pegamos el siguiente fragmento para nuestra instancia de Base de datos:</h5>
mariadb:
    image: mariadb
    environment:
     - MYSQL_ROOT_PASSWORD=password
     - MYSQL_DATABASE=wordpress
     - MYSQL_USER=user
     - MYSQL_PASSWORD=password
    volumes:
     - ./database:/var/lib/mysql

<h5>Finalmente levantamos nuestros proyectos</h5>

- docker-compose up -d

Una vez finalizado el comando anterior deberá ir a al navegador y escribir la dirección ip pública de su máquina, vera una pantalla como la siguiente:

![image](https://user-images.githubusercontent.com/43093044/135964235-87f0fc7d-e295-435b-bb73-a320deb016ac.png)

Deberá proceder con la instalación y configuración de Wordpress.

<h3>Configuración registros DNS</h3>
Una vez tienes un dominio debes configurar para que este apunte a la dirección ip de tu máquina, en nuestro caso tenemos el dominio en SiteGround, donde debemos configurar los servidores de nombres.
![image](https://user-images.githubusercontent.com/43093044/135964815-a3b19802-88dd-4dc2-ad71-6109a1c30925.png)

Nuestra configuración de DNS y está cloudflare:

![image](https://user-images.githubusercontent.com/43093044/135964594-e288f85c-872b-4080-89a2-b3702dc2f25a.png)

<h3>Configuración Certificado SSL</h3>

En nuestro caso dentro de cloudFlare debemos ir a SSL/TLS

![image](https://user-images.githubusercontent.com/43093044/135965071-c91e70a6-e73e-4601-b0f3-aeefc801b24d.png)

Y seleccionamos la opción "Flexible".

![image](https://user-images.githubusercontent.com/43093044/135965182-259bd97e-bf4b-4b7a-ab42-d9e6f6138508.png)


<h3>Cambio dirección en Worpress</h3>


Debemos ir a Wordpress y en Ajustes -> Generales cambiar nuestras URL por las de nuestro dominio

![image](https://user-images.githubusercontent.com/43093044/135965411-8a98c9f8-09fb-4bc2-894d-8cb3d8730c8c.png)

<h1>Escalabilidad</h1>

<h3>- Load balancer AWS </h3>

Debemos escoger el application load balancer

![image](https://user-images.githubusercontent.com/43093044/138543768-3fb1044b-0789-43e4-97f8-8bbe9f3d1b82.png)

Realizamos la configuración de nuestro balanceador de carga y luego procedemos a crear una plantilla (imagen) de nuestra aplicación, la cual será replicada.

![image](https://user-images.githubusercontent.com/43093044/138545675-d7934f59-3b82-4430-981e-0fa2d36c89fe.png)

<h3>Ahora debemos configurar nuestro auto Scaling Group</h3>

![image](https://user-images.githubusercontent.com/43093044/138545727-f47c2e90-3f08-4ee2-b4d2-2061018e4f4c.png)

![image](https://user-images.githubusercontent.com/43093044/138545877-e434c40c-9d6d-4d27-89e7-fa85ed211ce1.png)


<h3>Debemos enlazar nuestro balanceador con nuestro auto scaling group</h3>

![image](https://user-images.githubusercontent.com/43093044/138545925-2c8295e9-d8b3-4c14-82b7-2c071be11f80.png)


En nuestras politicas configuraremos para que en el caso de que el uso de la cpu supere el 70% despues de 5 minutos, creará una nueva instancia.

![image](https://user-images.githubusercontent.com/43093044/138546004-76ada657-1f8b-4459-9ed2-15e768d27a92.png)

Ahora podemos crear notificaciones para que aws nos mantenga informados

![image](https://user-images.githubusercontent.com/43093044/138546018-ced32972-2676-4ba1-9503-342b86c160c1.png)


<h3>Referencias</h3>

- https://docs.docker.com/engine/install/
- https://upcloud.com/community/tutorials/deploy-wordpress-with-docker-compose/
- https://dash.cloudflare.com/
- https://my.siteground.com/
- https://www.youtube.com/watch?v=HPXdwErNahk&ab_channel=YoelvisMulen%7Bcode%7D
- https://www.youtube.com/watch?v=WLu41jAYjYk&ab_channel=JavaHomeCloud
