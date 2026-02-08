# Immich

## Instalación:
- Instala docker
- Crea una carpeta para el docker-compose de Immich:
    ```bash
        mkdir -p ~/immich
        cd ~/immich
    ```
- Obten el docker-compose:
    ```bash
        wget -O docker-compose.yml https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml
    ```
- Obten el ejemplo de .env (saldra en la carpeta aunque no se liste con `ls`):
    ```bash
        wget -O .env https://github.com/immich-app/immich/releases/latest/download/example.env
    ```
- Edita el .env con `nano .env` además debes editar estas lineas especificamente:
```bash
    UPLOAD_LOCATION=/mnt/data/immich/library
    # The location where your database files are stored. Network shares are not supported for the database
    DB_DATA_LOCATION=./postgres
    # To set a timezone, uncomment the next line and change Etc/UTC to a TZ identifier from this list: https://en.wikipedia>
    TZ=America/Lima
    # The Immich version to use. You can pin this to a specific version like "v2.1.0"
    IMMICH_VERSION=v2
    # Connection secret for postgres. You should change it to a random password
    # Please use only the characters `A-Za-z0-9`, without special characters or spaces
    DB_PASSWORD=contraseña
```
- Inicia el contenedor:
    ```bash
        docker compose up -d
    ```
- Entra a http://ip_server:2283

## Como hacer copias de seguridad de la base de datos en otro disco
- Las copias de seguridad de la bse de datos se guardan automaticamente en la ubicacion de subida (UPLOAD_LOCATION/backups) por lo que como ahí tenemos raid 1 no hay problema