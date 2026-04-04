# Adguard home

## Instalación
- Crea una carpeta para el compose y entra a ella:
    ```bash
        mkdir adguard && cd adguard
    ```
- Crea el archivo `nano docker-compose.yml` y dentro pon:
    ```bash
    services:
        adguardhome:
            image: adguard/adguardhome:latest
            container_name: adguardhome
            restart: unless-stopped
            # Volúmenes para que tus filtros y configuraciones no se borren
            volumes:
                - ./workdir:/opt/adguardhome/work
                - ./confdir:/opt/adguardhome/conf
            # Mapeo de puertos necesarios
            ports:
                - "53:53/tcp"
                - "53:53/udp"
                - "80:80/tcp"    # Panel de control y HTTP
                - "443:443/tcp"  # HTTPS / DNS-over-HTTPS
                - "443:443/udp"  # DNS-over-QUIC
                - "3000:3000/tcp" # Instalador inicial
                - "853:853/tcp"  # DNS-over-TLS
                - "784:784/udp"  # DNS-over-QUIC
            networks:
                - adguard_net

    networks:
        adguard_net:
            driver: bridge
    ```
- Levanta el contenedor:
    ```bash
        docker compose up -d
    ```
- Si da algún error referente a un puerto ocupado puedes ver que servicios lo están usando (cambiar el puerto o simplemente quitarlo del compose si no lo usarás):
    ```bash
        sudo ss -tulpn | grep :53
    ```
