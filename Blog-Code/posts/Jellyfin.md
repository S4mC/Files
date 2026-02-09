# Jellyfin

- Instala docker
- Crea una carpeta para el docker-compose de Jellyfin:
    ```bash
        mkdir -p ~/jellyfin
        cd ~/jellyfin
    ```
- Crea un docker compose con `nano docker-compose.yml`:
    ```bash
        services:
            jellyfin:
                image: jellyfin/jellyfin
                container_name: jellyfin
                user: 1000:1000
                ports:
                    - "8096:8096"
                volumes:
                    - ./config:/config
                    - ./cache:/cache
                    - /mnt/data/nextcloud/data/data/admin/files/Music:/media/music/admin:ro # lo mismo para cada usuario
                restart: "unless-stopped
    ```
- Da los permisos a la carpeta:
```bash
    sudo chown -R 1000:1000 ~/jellyfin
```
- Como esta corriendo con nuestro usuario entonces añadete al grupo www-data para tener acceso a las carpetas data de nextcloud:
```bash
    sudo usermod -aG www-data sam
    newgrp www-data
```

- Inicia el docker compose:
    ```bash
        docker-compose up -d
    ```
