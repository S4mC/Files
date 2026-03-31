# Harbor
## Instalar Harbor en server debiam
- Descarga el paquete:
    Ve a la página de [releases new](https://github.com/goharbor/harbor/releases) y copia el enlace del instalador offline (ej. v2.15.0).
        ```bash
            wget https://github.com/goharbor/harbor/releases/download/v2.15.0/harbor-offline-installer-v2.15.0.tgz
            tar xvzf harbor-offline-installer-v2.15.0.tgz
            cd harbor
        ```
- Copia la plantilla: `cp harbor.yml.tmpl harbor.yml`
- Edita el archivo: `nano harbor.yml`
    - hostname: Pon tu dominio (ej: hub.tudominio.com).
    - http: Pon un puerto que no uses (como 8010 digamos).
    - https: Comenta toda esta sección (pon # al inicio de las líneas). Cloudflare se encargará del SSL, por lo que Harbor recibirá tráfico local en HTTP.
    - data_volume: Pon el lugar donde se guardarán las imágenes
    - external_url: Necesarío si usas ngix y cloudfare tunnel para evitar errores
- Instalación de Harbor
    Ejecuta el script de instalación. Puedes añadir --with-trivy para escanear vulnerabilidades en tus imágenes:
    `sudo ./install.sh --with-trivy`
- Configura en ngix (ponle client_max_body_size 5G; para que funcione bien) y cloudfare tunnel y ahora entrando desde el hostname deberías poder iniciar (por defecto usuario=admin y contraseña=Harbor12345) (Te saldra error si intentas acceder desde fuera del hostname incluso si pones bien todo)

## Como subir imagenes a Harbor
Antes de subir cualquier imagen, Harbor requiere que exista un "Proyecto" para organizarlas.
- Accede a la interfaz web de tu instancia de Harbor.
- Ve a Projects > New Project.
- Dale un nombre (ej. mi-proyecto) y decide si será público o privado.

:::details ⚠️ Forma de hacer que se pueda subir a traves de tailscale directamente al servidor para evitar el limite de 100MB de cloudfare
	Requisitos:
    - Necesitas tener un certificado SSL origins configurado para ese subdominio desde cloudfare
    - Necesitas poder editar el host del dispositivo en donde estas configurando esto (en este casso windows)
    - Necesitas tener tailscale configurado y funcionando en tu pc que se conecte al servidor
    
    Lo que haremos es que, solo para tu computadora, tu dominio de Harbor apunte a la IP de Tailscale de tu servidor Debian en lugar de a la IP de Cloudflare
    - Primero modificaremos el host, en windows presionas Windows + R y escribes:
        ```language
            %windir%\system32\drivers\etc\
        ```
    - Copias el archivo green`hosts` y lo pegas en el escritorio.
    - Lo abres con el block de notas y al final pegas (reemplaza primero):
        ```language
            tu.ip.del.server.en.tailscale hub.tu.dominio
        ```
    - Guarda el archivo, copialo y pegalo en la carpeta de donde lo copiamos (%windir%\system32\drivers\etc\) (si pide permisos para reemplazar aceptalos)
    
    Ahora cuando hagas docker push hub.tu.dominio/..., tu computadora no irá a internet (donde está el límite de 100MB de Cloudflare) sino se conectará directamente al servidor.
    - Ahora vuelve a entrar a Harbor desde el navegador (podría decirte que no es seguro y no dejarte entrar, si es así entra desde modo incognito o trata de borrar lso datos de la página y volver a entrar)
    - Una vez dentro de la página has clic en "Ver información del sitio" (O "No seguro", al lado de la URL)
    - Clic en "La conexión es segura" > "El certificado es válido" (O "Detalles del certificado")
    - En la ventana que se abre clic en "Detalles" y luego en "Exportar".
    - Guarda el certificado en el escritorio (o cualquier otra carpeta) y luego abrelo (doble clic)
    - Clic en "Instalar certificado...".
    - Selecciona Usuario actual y dale a Siguiente.
    - IMPORTANTE: Selecciona "Colocar todos los certificados en el siguiente almacén".
    - Haz clic en Examinar y elige "Entidades de certificación raíz de confianza".
    - Dale a Aceptar, Siguiente y Finalizar. Te saldrá un aviso de seguridad de Windows, dile que Sí.

    Una vez instalado el certificado, reinicia Docker Desktop y entonces debería reconocer la conexión como segura, incluso yendo por la IP de Tailscale. **(prueba eso en los siguientes pasos)**
:::

Debes autenticar tu cliente Docker con el servidor de Harbor. Ejecuta el siguiente comando ingresando tu dominio o IP
```bash
    docker login hub.tu-dominio.com
```
Docker necesita saber exactamente a dónde enviar la imagen. Para ello, debes renombrarla siguiendo este formato: dominio-harbor/proyecto/imagen:etiqueta.
- Si ya tienes una imagen local llamada mi-app:v1, el comando sería:
    ```bash
        docker tag mi-app:v1 harbor.tu-dominio.com/mi-proyecto/mi-app:v1
    ```
- Finalmente, envía la imagen al repositorio de Harbor:
    ```bash
        docker push harbor.tu-dominio.com/mi-proyecto/mi-app:v1
    ```
- Si todo esto funciona sin errores entonces estás listo para pasar imagenes (además funciono la conexión directa a tailscale si la intentaste)