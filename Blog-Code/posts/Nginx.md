# Nginx UI

## Como instalar Nginx en debiam y usar una interfaz visual
- Ejecuta este comando en la terminal del servidor para instalar nginx y nginxUI:
  ```bash
    sudo apt update && sudo apt install nginx
    sudo bash -c "$(curl -L https://cloud.nginxui.com/install.sh)" @ install
  ```
- Luego para acceder al panel, abre tu navegador y escribe la dirección IP de tu servidor seguida del puerto 9000 (que es el puerto por defecto).
    Ejemplo: http://tu-ip-servidor:9000
- Una vez que pruebes que funciona bien debes de cambiar de ubicación los datos para que no se pierdan en un reinicio (me paso eso solo con los usuarios ACME clave privada)
- Ejecuta:
    ```bash
        sudo mkdir -p /usr/local/etc/nginx-ui
        sudo chown -R root:root /usr/local/etc/nginx-ui
        sudo chmod 755 /usr/local/etc/nginx-ui
        sudo nano /etc/systemd/system/nginx-ui.service
    ```
- Busca la línea que dice purple`WorkingDirectory` y cámbiala por otra ruta:
    ```nano
        WorkingDirectory=/usr/local/etc/nginx-ui
    ```
- Reinicia nginx UI
    ```bash
        sudo systemctl daemon-reload
        sudo systemctl restart nginx-ui
    ```
- Ejecuta:
    ```bash
        sudo nano /usr/local/etc/nginx-ui/app.ini
    ```
- Busca la línea en purple`[cert]` que dice purple`RecursiveNameservers` y cámbiala por (Reemplaza a los NS(?=ns-donde-buscar) correctos primero):
    ```nano
        RecursiveNameservers = tu-ns.ns.cloudflare.com,tu-2-ns.ns.cloudflare.com
    ```
    :::float-ns-donde-buscar
        Deben estar en la página de tu registrador de dominio, donde compraste el dominio. En Cloudflare pueden salir unos NS pero pueden ser diferentes a los de Cloudflare.
    :::
- Reinicia nginx UI
    ```bash
        sudo systemctl daemon-reload
        sudo systemctl restart nginx-ui
    ```
- Puede que sea necesario que vuelvas a añadir al ACME user o los certificados de Cloudflare DNS si los habías puesto antes (eliminalos y vuelvelos a crear)

## Obtener certificados automáticamente con Let's Encrypt
Let's Encrypt permite tener certificados gratuitos, para sacarlos automáticamente con Nginx UI debes:
- En Nginx UI "Cetificates" > "ACME User" crea un nuevo usuario:
    - Name: Let's Encrypt
    - Email: tu-correo@gmail.com
    - CA Dir: https://acme-v02.api.letsencrypt.org/directory
    - Register On Startup: true
- En el panel de Cloudflare "API Tokens" > "Create Token"
    - Usa la plantilla "Edit zone DNS"
    - En "Permissions" pon:
        - Zone DNS Edit
        - Zone DNS Read
    - En "Zone Resources" puedes elegir todas las zonas o solo un dominio específico
    - Clic en "Continue to summary" y copia el token 
- En Nginx UI "DNS" > "Credentials" crea un nuevo credencial (usando el token de Cloudflare DNS):
    - Name: Cloudfare DNS Credencial
    - DNS Provider: Cloudfare
    - CF_DNS_API_TOKEN: cfut_tu_credencial
    - CF_ZONE_API_TOKEN: cfut_tu_credencial
- En Nginx UI "Cetificates" > "Certificates List" clic en "Issue Certificate", elige el dominio (o para todos los subdominios con Wildcard) luego pon:
    - Challenge Method: DNS01
    - Key Type: EC256
    - ACME User: Let's Encrypt
    - Credential: Cloudfare DNS Credencial
- Guarda los cambios y prueba que funcione, se repetirá automáticamente según esté configurado en la configuración de Nginx UI