# Next Cloud

## Instalar nextcloud en server debiam:
- En el server instala docker:
  ```bash
    sudo apt update
    sudo apt install -y docker.io docker-compose
    sudo systemctl enable --now docker
  ```

- Crea una raid 1 en `/mnt/data` si deseas para evitar la perdida de los archivos:
    :::details -compact Usar raid 1 (2 discos con archivos duplicados)
        - Lista los discos `lsblk`
        - Verifica que los discos no esten montados (en este caso discos a y b, tienes que poner la letra de los discos a los que poner raid 1):
            ```bash
                mount | grep sd[ab]
            ```
            *No debería de salir nada, si sale algo desmontalos*
        - Instala mdadm que permite crear raid facilmente:
            ```bash
                sudo apt update
                sudo apt install mdadm
            ```
        - Creamos la raid de nivel 1 (md0) con las particiones correctas (sda3 y sdb1):
            ```bash
                sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sda3 /dev/sdb1
            ```
            *write-indent bitmap permite recuperación mucho mas veloz a cambio de una pequeña disminucion en la escritura de datos, yo en este casi si lo activo (porque mis discos son grandes)*
        - Formatea la raid 1:
            ```bash
                sudo mkfs.ext4 /dev/md0
            ```
        - Montar la raid 1 como una carpeta (data):
            ```bash
                sudo mkdir /mnt/data
                sudo mount /dev/md0 /mnt/data
            ```
        - Guardar la raid en green`/etc/mdadm/mdadm.conf`, ejecuta:
            ```bash
                sudo mdadm --detail --scan | grep -v -f /etc/mdadm/mdadm.conf | sudo tee -a /etc/mdadm/mdadm.conf
                sudo update-initramfs -u
            ```
        - Hacer el montaje permanente, obtén el uuid del raid: `lsblk -f /dev/md0`, luego ejecuta:
            ```bash
                sudo nano /etc/fstab
            ```
            En el editor nano que se abre escribe esta linea al final: `UUID=xxxx-xxxx  /mnt/data  ext4  defaults  0  2` 
    :::

- Crea un carpeta dedicada en el server:
  ```bash
    mkdir -p ~/nextcloud
    cd ~/nextcloud
  ```

- Crea el docker compose para nextcloud:
  ```bash
    nano docker-compose.yml
  ```

- Dentro pega (reemplazando antes la "contraseña", "JWT_SECRET_KEY", "NEXTCLOUD_URL", "CLAVE_SUPER_SEGURA", "aliasgroup1", "server_name" y "NC_HAPROXY_PASSWORD"):
  ```bash
    services:
      db:
        container_name: nextcloud-postgres
        image: postgres:16
        restart: always
        volumes:
          - pgdata:/var/lib/postgresql/data
        environment:
          POSTGRES_DB: nextcloud
          POSTGRES_USER: postgre
          POSTGRES_PASSWORD: contraseña

      redis:
        container_name: nextcloud-redis
        image: redis:7-alpine
        restart: always
        command: redis-server --save "" --appendonly no

      app:
        container_name: nextcloud-nextcloud
        image: nextcloud:apache
        restart: always
        ports:
          - "8080:80"
        volumes:
          - /mnt/data/nextcloud/data/:/var/www/html
        environment:
          POSTGRES_HOST: db
          POSTGRES_DB: nextcloud
          POSTGRES_USER: postgre
          POSTGRES_PASSWORD: contraseña
        depends_on:
          - db
          - redis

      nextcloud-whiteboard-server:
        container_name: nextcloud-whiteboard
        image: ghcr.io/nextcloud-releases/whiteboard:stable
        restart: always
        ports:
          - "3002:3002"
        environment:
          - NEXTCLOUD_URL=https://nextcloud.tudominio.com
          - JWT_SECRET_KEY=TuClaveSecretaJWT
          - MAX_UPLOAD_FILE_SIZE=10485760
      
      collabora:
        container_name: nextcloud-collabora
        image: collabora/code
        restart: always
        ports:
          - "9980:9980"
        environment:
          - username=admin
          - password=CLAVE_SUPER_SEGURA
          - aliasgroup1=https://nextcloud.tudominio.com:443
          - extra_params=--o:ssl.enable=false --o:ssl.termination=true
          - DONT_GEN_SSL_CERT=true
          - server_name=collabora.tudominio.com
          - dictionaries=es_ES
        cap_add:
          - MKNOD
          
      nextcloud-appapi-dsp:
        image: ghcr.io/nextcloud/nextcloud-appapi-dsp:release
        container_name: nextcloud-appapi-dsp
        restart: always
        environment:
          - NC_HAPROXY_PASSWORD=contraseña
          - NC_INSTANCE_URL=https://nextcloud.tudominio.com
        ports:
          - "2375:2375"
        volumes:
          - /var/run/docker.sock:/var/run/docker.sock

    volumes:
      pgdata:
  ```

- Añade tu usuario al grupo docker:
  ```bash
    sudo usermod -aG docker sam
  ```

- Cerrar sesión y volver a entrar o:
  ```bash
    newgrp docker
  ```

- Inicia el docker compose:
  ```bash
    docker compose up -d
  ```

- Como hemos instalado redis entonces debemos de configurarlo, para eso entra al contenedor de nextcloud
  ```bash
    docker exec -it nextcloud-nextcloud bash
  ```
- Instalar nano
  ```bash
    apt update && apt install -y nano
  ```
- Entra a la configuración
  ```bash
    nano config/config.php
  ```
  Y dentro añade (memcache.local puede que ya este con ese valor, si es asi solo añade los demás):
  ```nano
    'memcache.local' => '\\OC\\Memcache\\APCu',
    'memcache.locking' => '\\OC\\Memcache\\Redis',
    'redis' => [
      'host' => 'redis', 'port' => 6379,
    ],
    'default_language' => 'es',
    'force_language' => 'es',
  ```
- Reinicia todo:
  ```bash
    docker compose down
    docker compose up -d
  ```
- Verifica que redis funciona:
  ```bash
    docker exec -u www-data nextcloud-nextcloud php occ config:list system | grep memcache
  ```
  Debe salir ` "memcache.locking": "\\OC\\Memcache\\Redis",`

- Entra a http://ip_o_localhost:8080

## Cambiar trusted domains en nextcloud:
- Entra al contenedor de nextcloud
  ```bash
    docker exec -it nextcloud-nextcloud bash
  ```
- Instalar nano
  ```bash
    apt update && apt install -y nano
  ```
- Entra a la configuración
  ```bash
    nano config/config.php
  ```
- Añade el dominio **sin hhtps**
- Listo!

## Cambiar almanenamiento de archivos nextcloud
- Anda a la carpeta del docker-compose `cd ~/nextcloud` y ejecuta `docker compose down` para apagar nextcloud
- Lista los volumenes que tienes en docker con `docker volume ls`
- Luego ejecuta:
    `docker volume inspect nombre_volumen_nextcloud`
    En "Mountpoint" estará la ubicación de los datos de nextcloud
- Instala rsync para pasar los archivos conservando permisos:
    ```bash
        sudo apt update
        sudo apt install rsync
    ```
- Copia los datos con sus permisos usando rsync a la nueva ubi (usa -Aav en lugar de -Aa para ver logs como errores):
    ```bash
        sudo mkdir -p "/mnt/data/nextcloud/data/"
        sudo rsync -Aa /var/lib/docker/volumes/nextcloud_nextcloud/_data/ /mnt/data/nextcloud/data/
    ```
- Entra al docker compose de nextcloud `nano docker-compose.yml` y quita el volumen "nextcloud" o el que tengas y a cambio pon la carpeta que creamos "/mnt/data/nextcloud/data/"
- Ejecuta `docker compose up` y verifica que todos los archivos se mantienen
- Si todo funciona bien elimina el anterior directorio de guardado: `sudo rm -rf /var/lib/docker/volumes/nextcloud_nextcloud/_data`

## Guardar copia de seguridad de base de datos sql en otro disco montado:
- Crea la otra carpeta que usaremos para la copia de seguridad: `sudo mkdir -p /mnt/sql`
- Crea un sistema de archivos con la particion del disco (en este caso sdc1): `sudo mkfs.ext4 /dev/sdc1`
- Monta el disco: `sudo mount /dev/sdc1 /mnt/sql`
- Obten el uuid del disco: `lsblk -f /dev/sdc1`
- Edita la fstab para que el montaje sea permanente, ejecuta: `sudo nano /etc/fstab` y al final pega:
    ```bash
        UUID=xxxx-xxxx  /mnt/sql  ext4  defaults,noatime  0  2
    ```
- Crea la carpeta para el backup y ponle permisos:
    ```bash
        sudo mkdir -p /mnt/sql/backups
        sudo chown $USER:$USER /mnt/sql/backups
    ```
- Lista los contenedores que tienes con: `docker ps`
- Con el nombre del contenedor (en este caso nextcloud-postgres) ejecuta: `docker exec nextcloud-postgres pg_dump -U postgre nextcloud | gzip > /mnt/sql/backups/nextcloud_$(date +%F_%H-%M).sql.gz`
- Verifica que los archivos se hayan creado con: `ls /mnt/sql/backups`
- Crear el script de backupts automaticas, ejecuta: `nano ~/backup_nextcloud_db.sh`, luego pega esto dentro:
    ```bash
        #!/bin/bash

        FECHA=$(date +%F_%H-%M)
        DESTINO="/mnt/sql/backups"
        CONTENEDOR="nextcloud-postgres"
        USUARIO="postgre"
        DB="nextcloud"

        mkdir -p "$DESTINO"

        docker exec "$CONTENEDOR" pg_dump -U "$USUARIO" "$DB" \
        | gzip > "$DESTINO/nextcloud_$FECHA.sql.gz"

        # borrar backups de más de 14 días
        find "$DESTINO" -type f -name "*.sql.gz" -mtime +14 -delete
    ```
- Da permisos de ejecución a cron: `chmod +x ~/backup_nextcloud_db.sh`
- Prueba el script con: `~/backup_nextcloud_db.sh` y `ls /mnt/sql/backups`
- Edita cron para añadir la ejecución automática del script: `crontab -e`, agrea al final:
    ```bash
        30 3 * * * /home/sam/backup_nextcloud_db.sh >> /home/sam/backup_db.log 2>&1
    ```
- Listo!, restaura una copia de seguridad con:
    ```bash
        # Poner en mantenimiento nextcloud
        docker exec -u www-data nextcloud-nextcloud php occ maintenance:mode --on
        # Lista las copias en la backup:
        ls -lh /mnt/sql/backups
    ```
    Copia el nombre de la ultima copia de seguridad, edita y ejecuta:
    ```bash
        docker exec nextcloud-postgres psql -U postgre -d postgres -c "DROP DATABASE IF EXISTS nextcloud;"
        docker exec nextcloud-postgres psql -U postgre -d postgres -c "CREATE DATABASE nextcloud;"
        # Cambia "nombre_nextcloud" por el nombre de la base de datos a resturar
        gunzip -c /mnt/sql/backups/nombre_nextcloud.sql.gz | docker exec -i nextcloud-postgres psql -U postgre nextcloud
    ```
    Si no hay errores quita el mantenimiento:
    ```bash
      docker exec -u www-data nextcloud-nextcloud php occ maintenance:mode --off
    ```


## Configurar "Ajustes básicos" > "Trabajos en segundo plano"

- En tu terminal (fuera de Docker), ejecuta:
  ```bash
    sudo crontab -e
  ```
- Añade la siguiente línea al final del archivo:
  ```bash
      */5 * * * * docker exec -u www-data nextcloud-nextcloud php -f /var/www/html/cron.php
  ```
- Luego en las opciones de la pestaña de nextcloud elige "cron", si no funciona verifica que el comandp `docker exec -u www-data nextcloud-nextcloud php -f /var/www/html/cron.php` si funcione (no arroje nada al ejecutar)


## "Avisos de seguridad y configuración" no carga ("Fallo al ejecutar las verificaciones de instalación")
- Puedes desde la terminal ejecutar:
  ```bash
      docker-compose exec --user www-data -e NC_loglevel=0 app php occ setupchecks
  ```

## Configurar pizarra Excalidraw en tiempo real
- En la lista de sitios de ngix puedes añadir una location al sitio de next cloud:
  ```bash
      # Configuración para el WebSocket de la Pizarra
      location /whiteboard/ {
          proxy_pass http://localhost:3002/; # Importante el "/" al final
          
          # Cabeceras necesarias para WebSockets
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection "upgrade";
          
          # Cabeceras estándar
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;

          # Evitar cortes de conexión por inactividad
          proxy_read_timeout 86400s;
          proxy_send_timeout 86400s;
      }
  ```
- Luego en la configuración añade la "Clave secreta JWT" del docker-compose y "wss://nextclouddominio.com/whiteboard/"


## Activar Nextcloud Office
- En ngix añade un nuevo sitio:
  - listen: `443 ssl`
  - listen: `[::]:443 ssl`
  - server_name: `collabora.tudominio.com`
  - Certificados ssl
  - En locations:
    `~ ^/cool/(.*)/ws$`
    ```bash
      proxy_pass http://localhost:9980;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "Upgrade";
      proxy_set_header Host $host;
      proxy_read_timeout 36000s;
    ```
    `^~ /cool/adminws`
    ```bash
      proxy_pass http://localhost:9980;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "Upgrade";
      proxy_set_header Host $host;
      proxy_read_timeout 36000s;
    ```
    `/`
    ```bash
      proxy_pass http://localhost:9980/;
      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $remote_addr;
      proxy_set_header X-Forwarded-Proto https;
      proxy_set_header X-Forwarded-Host $host;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    ```
- Configura el tunnel con cloudfare
- Anda a la configuración de administrador de nextcloud y entra a "Office"
- Elije "Use su propio servidor", dentro pega `http://nextcloud-collabora:9980` y activa el checkbox
- En "Allow list for WOPI requests" pon la ip de tu servidor o dejalo en blanco (primero en blanco y si todo funciona puedes modificarlo y probar)

## Como habilitar Daemons de despliegue (AppAPI)

- Entra en la configuración de administrador y a "AppAPI"
- Clic en "Registrar Daemon"
- En la pestaña que se abre selecciona "Docker Socket Proxy"
- En "Servidor del Daemon" escribe "nextcloud-appapi-dsp:2375"
- En "Contraseña de HaProxy" escribe la clave que pusiste en el campo "NC_INSTANCE_URL" del docker-compose
- Y dentro de "Configuración del despliegue" en "Red de Docker" escribe "nextcloud_default"
- Verifica la conexión y si funciona clic en "Registrar"

## Instalar modelo de ia local en server:
- Instala ollama: `curl -fsSL https://ollama.com/install.sh | sh`
- Instala un modelo (en este caso Llama 3.2 3B): `ollama run llama3.2:3b`
- Ejecutar Ollama como servicio API:
    - Edita: `sudo systemctl edit ollama`
    - Pega entre los comentarios (ten cuidado de pegarlo debajo del que dice que debajo se borra):
        ```bash
            [Service]
            Environment="OLLAMA_HOST=0.0.0.0:11434"
        ```
    - Reinicia:
        ```bash
            sudo systemctl daemon-reexec
            sudo systemctl restart ollama
        ```
    - Verifica: `ss -tulnp | grep 11434`, debe salir `0.0.0.0:11434`
- Instala la aplicación en NextCloud "OpenAI and LocalAI integration"
- En la configuracion de administrador > "Asistente" busca "OpenAI and LocalAI integration" y dentro:
    - En "Service URl" pon: `http://172.17.0.1:11434/v1`
    - Y en "Tiempo de vida de la solicitud" pon: `600`