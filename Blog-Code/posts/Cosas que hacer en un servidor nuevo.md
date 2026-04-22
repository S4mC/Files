# Cosas que hacer en un servidor nuevo

## Actualizar paquetes:
  ```bash
      sudo apt update && sudo apt upgrade -y
  ```

## Conectarse con ssh (si no está instalado)
- En la terminal del servidor debes de instalar ssh:
  ```bash
    sudo apt install openssh-server
  ```
- En la terminal del servidor inicia ssh (si sale error ejecuta `sudo ssh-keygen -A`):
```bash
    sudo systemctl start ssh
```
- En la terminal desde la que quieres conectarte ejecuta:
```bash
    ssh usuario_linux@ip_local_o_url
```

## Añadir un usuario a los sudoers
- Entra como superusuario:
  ```bash
      su
  ```
- Dentro ejecuta:
```bash
    sudo usermod -aG sudo nombre_usuario_a_admin
```
- Actualiza: 
```bash
    newgrp sudo
```
- Sal del superusuario `exit`
- Sal del usuario normal `exit`, cuando vuelvas a entrar ya estará el usuario en los sudoers

## Desactivar ahorro de energia linux debiam
- Desactiva los servicios de hibernación, suspendido y dormir:
  ```bash
    sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
  ```
- Luego puedes probar que funcionó con bash`systemctl status sleep.target` debe salir masked, inactive (dead)

## Hacer que discos avisen antes de fallar o cuando fallaron

### Si tienes un raid con mdadm
- Edita el archivo de configuración para añadir tu correo:
  ```bash
      sudo nano /etc/mdadm/mdadm.conf
  ```
- Busca purple`MAILADDR root`, cambialo por purple`MAILADDR tu_correo@gmail.com` y guarda el archivo
- Activa green`mdmonitor`:
  ```bash
      sudo systemctl enable mdmonitor --now
  ```
- Verifica que este corriendo (debe salir purple`active (running)`):
  ```bash
      systemctl status mdmonitor
  ```
- Instalar servicio de correo y activa el soporte para AppArmor:
  ```bash
      sudo apt update && sudo apt install msmtp msmtp-mta mailutils -y
  ```
- Entra a la configuración bash`sudo nano /etc/msmtprc` y pega esto (Reemplaza primero):
    ```nano
        defaults
        auth           on
        tls            on
        tls_trust_file /etc/ssl/certs/ca-certificates.crt
        logfile        ~/.msmtp.log

        account        gmail
        host           smtp.gmail.com
        port           587
        from           tu_correo@gmail.com
        user           tu_correo@gmail.com
        password       TU_PASSWORD_DE_APLICACION
        account default : gmail
    ```
- OPCIONAL asegura el archivo por tener contraseña: `sudo chmod 600 /etc/msmtprc`
- Prueba que llegue el correo: 
  ```bash
      sudo mdadm --monitor --scan --test --oneshot
  ```

### Para discos sueltos
- Debes tener configurado green`msmtprc`
- Instala smartmontools:
  ```bash
      sudo apt install smartmontools
  ```
- Habilitalo:
  ```bash
      sudo systemctl enable smartmontools --now
  ```
- Edita la configuración bash`sudo nano /etc/smartd.conf` y dentro pega o edita la linea (Reemplaza primero):
    ```nano
        DEVICESCAN -a -o on -S on -n standby -W 4,45,50 -m tu_correo@gmail.com -M test
    ```
- Reinicia el servicio:
  ```bash
      sudo systemctl restart smartmontools
  ```
- Si todo esta configurado bien debería llegar un correo de prueba inmediatamente
- Si todo funciona quita purple`-M test` de smart.conf para que no sea solo un correo de prueba

## Conectarse al servidor con una red privada virtual (incluso desde lejos)
Puedes instalar Tailscale, Netbird o otros servicios que permitan crear una red privada virtual de forma gratuita, eso permite conectarse con ssh desde otra ubicación de manera privada como si estuvieras en la misma red (te conectas con la ip asignada en la red privada creada)

## Optimizar
Puedes ver una guía para optimizar servidor que creamos aquí:
https://s4mc.github.io/Blog-Code/post.html?path=Mejoras%20a%20servidor