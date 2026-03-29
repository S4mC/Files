# Mejoras a servidor
## Activar BBR (Mejora de Red)
- Edita el archivo de configuración:
    ```bash 
        sudo nano /etc/sysctl.conf
    ```
- Pega estas líneas al final:
    ```bash 
        net.core.default_qdisc = fq
        net.ipv4.tcp_congestion_control = bbr
    ```
- Guarda y aplica los cambios:
    ```bash 
        sudo sysctl -p
    ```


## Optimización de Escritura en Disco (noatime)
Por defecto, Linux anota la hora exacta cada vez que se "lee" un archivo. Esto genera escrituras innecesarias. Cambiarlo a noatime acelera la lectura de archivos y alarga la vida de tus SSD/SBCs.
- Edita tu tabla de particiones:
    ```bash 
        sudo nano /etc/fstab
    ```
- Busca la línea de tu disco principal (donde está el símbolo /). Verás algo como:
    ```bash 
        defaults,errors=remount-ro
    ```
    ó
     ```bash 
        defaults
    ```
    ó
     ```bash 
        errors=remount-ro
    ```
- Añade noatime a cada partición que necesites, por ejemplo cambia a:
    ```bash
        defaults,noatime,errors=remount-ro
    ```
    ó 
    ```bash
        defaults,noatime
    ```
    ó
    ```bash
        noatime,errors=remount-ro
    ```
- Para aplicar sin reiniciar (solo ejecutalo con las particiones que se cambiaron):
    ```bash
        sudo mount -o remount /
        sudo mount -o remount /mnt/data

        sudo systemctl daemon-reload
    ```
- Para probar que se aplico bien ejecuta `cat /proc/mounts | grep " / "` y deberías ver green`noatime` en la lista de opciones


## Ajustar la "Agresividad" de la SWAP (swappiness)
La green`swappiness` controla qué tan dispuesto está Linux a usar el swap **(memoria virtual en disco)** en lugar de la RAM.
| Valor | Comportamiento |
|----------|----------|
| 0 | Evita swap casi completamente |
| 10 | Usa swap solo cuando es realmente necesario |
| 60 | Valor por defecto de Debian/Ubuntu |
| 100 | Usa swap agresivamente |

En este caso usaremos swappiness = 10:
- Edita el archivo:
    ```bash
        sudo nano /etc/sysctl.conf
    ```
- Agrega o modifica esta línea:
    ```bash
        vm.swappiness = 10
    ```
- Aplica el cambio:
    ```bash
        sudo sysctl -p
    ```


## Limpieza de Servicios Innecesarios:
Si no usas Bluetooth, impresión (CUPS) o descubrimiento de red (Que los dispositivos en tu red local se encuentren entre sí sin necesidad de un servidor DNS ni configuración manual), tener esos servicios activos solo consume RAM y CPU (aunque sea poco) y abre huecos de seguridad innecesarios.
- Para desactivarlos ejecuta:
    ```bash
        sudo systemctl disable --now bluetooth
        sudo systemctl disable --now cups
        sudo systemctl disable --now avahi-daemon
    ```


## Límites de archivos abiertos
Si tienes varios contenedores o servicios (como bases de datos y servidores web), el límite por defecto de archivos abiertos puede quedarse corto y causar errores de "Too many open files".
- Para evitar eso edita el archivo:
    ```bash
        sudo nano /etc/sysctl.conf
    ```
- Agrega:
    ```bash
        fs.file-max = 2097152
    ```
- Guarda y luego aplica executando:
    ```bash
        sudo sysctl -p
    ```
