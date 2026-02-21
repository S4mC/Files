# Chatwoot

- Instala docker
- Crea una carpeta para el docker-compose de Funkwhale:
    ```bash
        mkdir -p ~/chatwoot
        cd ~/chatwoot        
    ```
- Descarga el docker compose oficial:
    ```bash
        wget https://raw.githubusercontent.com/chatwoot/chatwoot/develop/docker-compose.production.yaml -O docker-compose.yml
    ```
- Crea archivo de entorno .env:
    ```bash
        wget https://raw.githubusercontent.com/chatwoot/chatwoot/develop/.env.example -O .env
    ```
- Edita el .env `nano .env`, dentro pon (reemplaza primero):
    ```bash
        FRONTEND_URL=https://chat.midominio.com
        SECRET_KEY_BASE=clave_muy_larga
        POSTGRES_PASSWORD=clave_fuerte
        REDIS_PASSWORD=clave_fuerte
    ```
- Modifica el docker compose `nano docker-compose.yml` (la clave de postgres, raid si usas "/mnt/data/chatwoot" y "127.0.0.1:3000:3000" a solo "3000:3000")
- Levanta el servicio:
    ```bash
        docker compose up -d
    ```
- Crea la base de datos:
    ```bash
        docker compose exec rails bundle exec rails db:prepare
        docker compose down
        docker compose up -d
    ```
- Accede al servicio en `http://IP_DEL_SERVIDOR:3000`