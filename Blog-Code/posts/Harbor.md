# Harbor
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
- Configura en ngix y cloudfare tunnel y ahora entrando desde el hostname deberías poder iniciar (por defecto usuario=admin y contraseña=Harbor12345) (Te saldra error si intentas acceder desde fuera del hostname incluso si pones bien todo)