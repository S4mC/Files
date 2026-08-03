# Autohostear un servidor de Minecraft

Es probable que tu proveedor de internet no te dé una IP pública real, sino una compartida mediante **CGNAT**.

Ejemplo:

- Tu router recibe una IP `100.x.x.x`
- Internet ve otra IP `190.x.x.x`

Eso significa que:

- No puedes abrir puertos
- Dynamic DNS no sirve
- Tu servidor no será accesible directamente desde internet

La solución es usar un **túnel inverso** (En este ejemplo se usará purple`Playit.gg`)


## MCSManager (Windows)

MCSManager permite crear y administrar servidores de Minecraft fácilmente mediante un panel web. Además es de **código abierto**.

### Instalación

1. Descargar MCSManager
2. Ejecutar el instalador
3. Abrir:
    http://localhost:23333
4. Crear el usuario administrador.

:::note
	MCSManager **no hace público el servidor**, únicamente lo administra.
:::


## Crear el servidor (Forge o Fabric)

- Si usarás **Forge** para poner mods, puedes descargar el instalador oficial, seleccionar **Create Server** y luego copiar el comando de inicio con java dentro de MCSManager.

- También puedes hacerlo directamente desde MCSManager:
    1. Clic en green`Create Server`
    2. Elegir:
        - Forge o Fabric.
        - Versión de Minecraft.
    3. Configurar:
        - Cantidad de RAM.
        - Puerto local (`25565`).
    4. Crear el servidor.


## Configurar el servidor

Edita el archivo `server.properties`:
```properties
    online-mode=false
    max-players=20
    render-distance=4
    simulation-distance=4
```
:::warning
	`server-ip` **siempre debe quedar vacío**.
:::


### Sistema de login (Opcional)

Como `online-mode=false`, cualquier jugador puede ingresar usando cualquier nombre.

Por ello es recomendable instalar un plugin o mod de autenticación.


### Instalar mods

En MCSManager abre la carpeta del servidor y en la subcarpeta green`mods` puedes poner mods green`.jar`  que sean:
- De la misma versión de Minecraft.
- Del mismo loader (Forge o Fabric).

Y para aplicar los cambios solo reinicia el servidor
:::note
	Si sale algún error es posible que sea porque falta una dependencia de los mods, si es así copia el error de la consola y pasaselo a una IA para que te diga que mod añadir (porque son dependencias) o cuales eliminar (porque son especificos para usuarios).


    Además los jugadores deben tener exactamente los mismos mods instalados y adicionalmente los específicos para usuarios.
:::


## Crear el túnel con Playit.gg

1. Crear una cuenta.
2. Descargar el **Playit Agent** para Windows.
3. Ejecutarlo.
4. Crear un nuevo túnel:
    - Tipo: `Minecraft Java`
    - Puerto local: `25565`
5. Copiar la dirección pública generada, ejemplo:
    ```url
        xxxx.playit.gg
    ```

:::note
    Playit Agent debe permanecer abierto mientras el servidor esté funcionando.
:::


## Iniciar el servidor

Desde MCSManager:

1. Iniciar el servidor.
2. Verificar que cargue sin errores.


## Administración básica

Desde la consola del servidor puedes ejecutar estos comandos:
```c
    list // Muestra la lista de jugadores conectados
    ban Nombre // Banea al jugador con ese nombre
    kick Nombre // Expulsa al jugador con ese nombre
    op Nombre // Otorga permisos de administrador al jugador con ese nombre
    deop Nombre // Quita los permisos de administrador al jugador con ese nombre
    banlist // Muestra la lista de jugadores baneados
```


## Reglas importantes

El servidor únicamente estará disponible si:
- La PC está encendida.
- MCSManager está abierto (por lo menos su consola).
- Playit Agent está ejecutándose.

## Flujo completo
```css
    Internet
        ↓
    Playit.gg
        ↓
    PC Windows
        ↓
    MCSManager
        ↓
    Servidor de Minecraft
```


## Mejorar el rendimiento

Para mejorar el rendimiento del servidor puedes:

- Usar **Chunky** para precargar chunks.
- Instalar mods de optimización.
- Aumentar la RAM mínima y máxima desde green`user_jvm_args.txt`.
- Reducir estas opciones en green`server.properties`:
    ```properties
        render-distance=4
        simulation-distance=4
    ```