# Como hacer aplicaciones web que corran en varios servidores sin downtime
Las aplicaciones web usarán un "Tunnel" de cloudflare con varios "Connectors", entonces cloudflare al recibir tráfico desde un dominio del tunnel (una "Route" del tunnel) redirigirá ese tráfico entre los connectors activos(?=distribucion-cloudflare-connectors), donde cada uno apunta a un servidor diferente.

:::float-distribucion-cloudflare-connectors
	La distribución de las peticiones entre los connectors depende de Cloudflare, pero normalmente el reparto es equitativo
:::

### En cada servidor
Como la route del tunnel apunta localmente entonces el connector de cada servidor apuntará a esa ubicación local, que en este caso apuntará a Nginx (un reverse proxy que maneja el tráfico)
- El green`Nginx de cada servidor debe tener corriendo todas las aplicaciones web` (exactamente las mismas)
- Las aplicaciones que corren en cada servidor deben estar diseñadas para poder funcionar sin estado(?=forma-aplicaciones-varios-servidores)
- Si se quiere actualizar una aplicación web en un servidor primero se desconecta el connector del servidor, se actualiza la app y se reconecta al connector


:::float-forma-aplicaciones-varios-servidores
	#### Funcionamiento del Backend y/o API:
    Como las APIs corren en ambos servidores, si una API necesita guardar un dato temporalmente entonces la otra API también debería verlo guardado temporalmente.
    - Por ejemplo, si se registra que un usuario está realizando una operación, que una solicitud ya fue procesada o que existe información en caché, ambas instancias deben compartir ese estado. Para lograrlo, es necesario utilizar un almacenamiento compartido como REDIS en lugar de la memoria local de cada servidor
:::

### Funcionamiento de las aplicaciones web
Como las aplicaciones van a estar duplicadas (corriendo exactamente el mismo código y versión en ambos servidores), entonces deben ser "stateless" (sin estado(?=stateless-aplicaciones)):
- Si necesita green`guardar o consultar un estado en el backend`, se hace desde Redis que corre de forma simultanea en todos los servidores y mantiene los datos en todos los servidores
    :::details-compact Redis
        Todas las réplicas mantienen una copia completa de los datos del maestro, sincronizada en tiempo real
        :::warning
            En este esquema solo el maestro acepta escrituras, las réplicas son de solo lectura
        :::
        - La replicación por sí sola no promueve automáticamente una réplica a maestro si el maestro se cae. Para eso se usa Sentinel, normalmente 3 instancias en servidores distintos.
        - Tus aplicaciones se conectan vía Sentinel (no directo a una IP fija), así saben siempre cuál es el maestro actual.
    :::
- Si se necesita green`guardar datos de la aplicación` eso se hace en PostgreSQL que corre de forma simultanea y mantiene los datos en todos los servidores
    :::details-compact PostgreSQL
        Todas las réplicas mantienen una copia completa de los datos del primario, sincronizada en tiempo real mediante el envío continuo del WAL (Write-Ahead Log)
        :::warning
            En este esquema solo el primario acepta escrituras, las réplicas son de solo lectura (hot standby)
        :::
        - La replicación por sí sola no promueve automáticamente una réplica a primario si este cae. Para eso se usa un gestor de failover como Patroni o repmgr, normalmente con un consenso distribuido (etcd, Consul o ZooKeeper) en servidores distintos.
        - Tus aplicaciones se conectan vía un proxy o balanceador (como HAProxy o PgBouncer apuntando al líder), no directo a una IP fija, así saben siempre cuál es el primario actual.
    :::
- Si un usuario sube green`archivos en un servidor` y este se cae entonces los demás servidores no podrán obtener el archivo. Las subidas deben ir a un almacenamiento compartido:
    :::details-compact Garage
        [Garage new](https://garagehq.deuxfleurs.fr) corre en todos los servidores, formando un único clúster. No es que cada servidor tenga una copia completa de todos los archivos (aunque puede haber réplicas), sino que Garage distribuye automáticamente los datos entre los nodos.
        Para eso es necesario que halla por lo menos 3 servidores y un numero impar de ellos (para que halla mayoria en los votos de garage)
    :::
- Para las green`tareas en segundo plano (Cron Jobs)` se suele usar un gestor de colas (que también puede usar Redis) para que un servidor tome la tarea y el otro sepa que ya está en proceso.
- Toda green`la caché dinámica debe vivir también en Redis`. Entonces, si un servidor invalida o actualiza un dato en caché, todos los servidores verán el cambio instantáneamente.
- La green`salida de logs` debe enviarse a un gestor de logs centralizado
- En general si un servidor se añade o se elimina, toda la aplicación web debe poder seguir funcionando sin perder ningun dato ni ningun problema

:::float-stateless-aplicaciones
	#### Tipo de API y el Frontend
    Cuando el Frontend se ejecute en el navegador del usuario y haga peticiones a la API, esa petición pasará por Cloudflare. Cloudflare la enviará a cualquiera de los dos servidores. Por lo tanto, la API del Servidor 1 o del Servidor 2 debe ser capaz de procesar la petición exactamente igual (no guardar estado en memoria sino en un almacenamiento compartido como REDIS).
:::
