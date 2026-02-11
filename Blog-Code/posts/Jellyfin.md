# Jellyfin

- Instala docker
- Crea una carpeta para el docker-compose de Jellyfin:
    ```bash
        mkdir -p ~/jellyfin
        cd ~/jellyfin
    ```
- Crea un docker compose con `nano docker-compose.yml` y dentro poner (primero reemplaza las variables y volumenes):
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
            
            jellyfin-watcher:
                image: alpine:latest
                container_name: jellyfin-watcher
                volumes:
                    # Montar el script
                    - ./watcher.sh:/watcher.sh
                    # Volumenes:
                    # Camara
                    - /mnt/data/nextcloud/data/data/admin/files/Camara:/watch/camara/admin:ro
                    - /mnt/data/nextcloud/data/data/admin2/files/Camara:/watch/camara/admin2:ro

                    # Musicas
                    - /mnt/data/nextcloud/data/data/admin/files/Musicas:/watch/musicas/admin:ro
                    - /mnt/data/nextcloud/data/data/admin2/files/Musicas:/watch/musicas/admin2:ro
                environment:
                    - JELLYFIN_URL=http://jellyfin:8096
                    - JELLYFIN_API_KEY=api_key
                command: sh /watcher.sh
                restart: unless-stopped
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

- Crea el archivo para monitorear las librerias `nano watcher.sh` y dentro pon (antes reemplaza con tus datos):
    ```bash -folded
        #!/bin/sh

        # Instalar dependencias
        apk add --no-cache inotify-tools curl jq

        echo "Monitoreando bibliotecas Jellyfin..."

        # Loop infinito
        while true; do
            # Esperar evento
            EVENT=$(inotifywait -r -q -e moved_from,delete,move,moved_to --format '%w%f %e' /watch)

            # --- FILTRO: Ignorar archivos temporales de Nextcloud ---
            case "$EVENT" in
                *.part|*/.*)
                    echo "Ignorando archivo temporal: $EVENT"
                    continue
                    ;;
            esac

            echo "Evento válido detectado: $EVENT"

            # Extraer la ruta del directorio del evento
            EVENT_PATH=$(echo "$EVENT" | awk '{print $1}')

            LIB=""
            LIB_PATH=""

            case "$EVENT" in
                */camara/admin/*)
                    LIB="Camara Admin"
                    LIB_PATH="/watch/camara/admin"
                    ;;
                */camara/admin2/*)
                    LIB="Camara Admin2"
                    LIB_PATH="/watch/camara/admin2"
                    ;;


                */musicas/admin/*)
                    LIB="Musicas Admin"
                    LIB_PATH="/watch/musicas/admin"
                    ;;
                */musicas/admin2/*)
                    LIB="Musicas Benjamin"
                    LIB_PATH="/watch/musicas/admin2"
                    ;;

            esac

            if [ -z "$LIB" ]; then
                echo "No coincide con ninguna biblioteca mapeada, ignorando..."
                continue
            fi

            # Obtener ID de la biblioteca
            ID=$(curl -s "${JELLYFIN_URL}/Library/MediaFolders" \
            -H "X-Emby-Token: ${JELLYFIN_API_KEY}" | \
            jq -r ".Items[] | select(.Path | test(\"/${LIB}(/|$)\")) | .Id")

            if [ -z "$ID" ] || [ "$ID" = "null" ]; then
                echo "Error: No se encontró ID para la biblioteca: $LIB. (Verifica el nombre en Jellyfin)"
                continue
            fi

            # **CONTAR ARCHIVOS EN LA BIBLIOTECA**
            FILE_COUNT=$(find "$LIB_PATH" -type f ! -name ".*" ! -name "*.part" 2>/dev/null | wc -l)


            # **CASO 1: Biblioteca vacía - Eliminar todos los items**
            if [ "$FILE_COUNT" -eq 0 ]; then
                echo "⚠️ Biblioteca vacía detectada: $LIB"

                # Obtener todos los items de esta biblioteca y eliminarlos
                ITEMS=$(curl -s "${JELLYFIN_URL}/Items?ParentId=$ID&Recursive=true&Fields=Path" \
                    -H "X-Emby-Token: ${JELLYFIN_API_KEY}" | \
                    jq -r '.Items[].Id')

                if [ -n "$ITEMS" ]; then
                    echo "$ITEMS" | while read -r ITEM_ID; do
                        if [ -n "$ITEM_ID" ] && [ "$ITEM_ID" != "null" ]; then
                            echo "Eliminando item: $ITEM_ID"
                            curl -X DELETE "${JELLYFIN_URL}/Items/$ITEM_ID" \
                                -H "X-Emby-Token: ${JELLYFIN_API_KEY}"
                        fi
                    done
                    echo "✓ Items eliminados de la biblioteca vacía."
                else
                    echo "No hay items para eliminar."
                fi

                continue
            fi

            # **CASO 2: Un solo archivo - Escaneo total de todas las bibliotecas**
            if [ "$FILE_COUNT" -eq 1 ]; then
                echo "⚠️ Solo 1 archivo detectado. Ejecutando escaneo TOTAL de todas las bibliotecas..."

                curl -X POST "${JELLYFIN_URL}/Library/Refresh" \
                    -H "X-Emby-Token: ${JELLYFIN_API_KEY}"

                echo "✓ Escaneo total iniciado."
                continue
            fi

            # **CASO 3: Más de un archivo - Escaneo específico de la biblioteca**
            echo "Biblioteca contiene $FILE_COUNT archivos. Escaneando biblioteca específica: $LIB"

            echo "Solicitando escaneo para ID: $ID ($LIB)"
            curl -X POST "${JELLYFIN_URL}/Items/$ID/Refresh" \
                -H "X-Emby-Token: ${JELLYFIN_API_KEY}"
            curl -X POST "${JELLYFIN_URL}/Items/$ID/Refresh?Recursive=true&MetadataRefreshMode=FullRefresh&ImageRefreshMode=FullRefresh&ReplaceAllMetadata=true&ReplaceAllImages=true" \
                -H "X-Emby-Token: ${JELLYFIN_API_KEY}"

            echo "Escaneo específico terminado."
        done
    ```

- Da permisos al script:
    ```bash
        chmod +x watcher.sh    
    ```

- Inicia el docker compose:
    ```bash
        docker-compose up -d
    ```