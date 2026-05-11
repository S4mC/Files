# Nginx

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