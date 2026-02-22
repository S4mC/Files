# n8n

- Instala docker
- Crea una carpeta para el docker-compose de n8n:
    ```bash
        mkdir -p ~/n8n && cd ~/n8n
    ```
- Crea el docker-compose `nano docker-compose.yml` dentro pega (reemplaza "N8N_BASIC_AUTH_PASSWORD" y la ubicación de data):
    ```bash
        services:
            n8n:
                image: n8nio/n8n:latest
                container_name: n8n
                restart: unless-stopped
                ports:
                    - "5678:5678"
                environment:
                    - TZ=America/Lima
                    - N8N_BASIC_AUTH_ACTIVE=true
                    - N8N_BASIC_AUTH_USER=admin
                    - N8N_BASIC_AUTH_PASSWORD=contraseña
                    - N8N_DIAGNOSTICS_ENABLED=false
                    - N8N_VERSION_NOTIFICATIONS_ENABLED=true
                volumes:
                    - /mnt/data/n8n:/home/node/.n8n
    ```
- Dale permisos a las carpetas usadas y inicialo:
    ```bash
        sudo chown -R 1000:1000 ~/n8n
        sudo mkdir -p /mnt/data/n8n
        sudo chown -R 1000:1000 /mnt/data/n8n
        docker compose up -d
    ```