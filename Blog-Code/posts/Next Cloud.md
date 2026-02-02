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
        - Hacer el montaje permanente, obtén el uuid del raid: `lsblk -f /dev/md0`, luego ejecuta:
            ```bash
                sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
                sudo update-initramfs -u
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

- Dentro pega:
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
              POSTGRES_PASSWORD: password

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
              POSTGRES_PASSWORD: password
            depends_on:
              - db

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

- Entra a http://ip_o_localhost:8080

- 