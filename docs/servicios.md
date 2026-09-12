# Servicios

La mayoría de servicios del homelab se ejecutan con Docker y Docker Compose. OpenClaw está instalado directamente sobre Ubuntu y Codex nativo trabaja sobre el sistema con los permisos de su usuario. Vixi Web usa además un entorno separado con permisos limitados.

## Resumen

| Área | Componentes | Estado | Función |
|---|---|---|---|
| Infraestructura | Pi-hole, WireGuard, wg-easy y Uptime Kuma | ✅ Implementado | Red local, acceso remoto privado y comprobación de disponibilidad. |
| Multimedia | Ruddarr, Radarr, Sonarr, Jackett, qBittorrent, Cloudflare WARP y Plex | ✅ Implementado | Gestionar las peticiones, buscar contenido, descargarlo, organizarlo y reproducirlo. |
| Monitorización | Prometheus, Grafana, Node Exporter, cAdvisor y smartctl-exporter | ✅ Implementado | Recoger y mostrar métricas del host, los contenedores y los discos. |
| Administración | Portainer, Homepage, Samba y Watchtower | ✅ Implementado | Administrar contenedores, acceder a los servicios, compartir datos en la LAN y aplicar actualizaciones autorizadas. |
| Servicios auxiliares | docker-socket-proxy y vixi-status | ✅ Implementado | Consultar el estado de Docker y del homelab sin dar acceso directo al socket. |
| Seguridad del sistema | UFW, Fail2ban y AppArmor | ✅ Implementado | Controlar accesos y aplicar protección al host y a los contenedores relevantes. |
| Automatización y agentes | OpenClaw, Vixi, Codex y Vixi Web | ✅ Implementado | Coordinar tareas y trabajar sobre proyectos con distintos niveles de permisos. |
| Delegación y especialización | Hermes | 🟡 En proceso | El agente ya existe, pero su arquitectura y sus futuros subagentes siguen en desarrollo. |

## Infraestructura

### Pi-hole

Pi-hole forma parte de los servicios base de la red local y se encarga del filtrado DNS para sus clientes.

### WireGuard y wg-easy

WireGuard proporciona acceso remoto privado al homelab. Los clientes de la LAN no pasan por la VPN: acceden directamente al servidor y a los servicios locales.

wg-easy se utiliza para administrar WireGuard. Los servicios administrativos no están pensados para exponerse directamente a Internet.

### Uptime Kuma

Uptime Kuma comprueba la disponibilidad de los servicios. Aparece también en monitorización porque cubre una función distinta a las métricas de Prometheus: permite saber si un servicio responde o deja de hacerlo.

## Multimedia

Los servicios multimedia forman un flujo único. Cada herramienta cubre una parte concreta y pasa el resultado a la siguiente:

1. **Ruddarr** envía la petición a Radarr o Sonarr.
2. **Radarr y Sonarr** solicitan la búsqueda a Jackett.
3. **Jackett** consulta los indexadores mediante Cloudflare WARP y devuelve los resultados a Radarr o Sonarr.
4. **Radarr y Sonarr** envían la descarga elegida a qBittorrent.
5. **qBittorrent** descarga mediante su conexión directa, sin VPN.
6. **Radarr y Sonarr** importan el contenido terminado y organizan las bibliotecas.
7. **Plex** consume las bibliotecas finales.

### Radarr y Sonarr

Sonarr administra las series y Radarr las películas. Ambos coordinan la búsqueda con Jackett, envían las descargas a qBittorrent y organizan el contenido cuando termina.

qBittorrent utiliza categorías separadas para que cada gestor pueda identificar e importar sus descargas.

### Jackett y Cloudflare WARP

Jackett actúa como intermediario entre Radarr o Sonarr y los indexadores. Cloudflare WARP se aplica únicamente a Jackett para resolver bloqueos de determinados indexadores.

Esta separación es intencionada: WARP no cubre todo el flujo multimedia.

### qBittorrent

qBittorrent recibe las descargas desde Radarr y Sonarr. Su tráfico mantiene salida directa y no utiliza VPN.

### Plex

Plex trabaja con las bibliotecas que Radarr y Sonarr ya han importado y organizado. No interviene en la búsqueda ni en la descarga.

## Monitorización

Prometheus centraliza las métricas que producen los exportadores. Grafana se utiliza para visualizarlas y Uptime Kuma comprueba la disponibilidad de los servicios.

| Componente | Qué aporta | Estado |
|---|---|---|
| Prometheus | Recoge métricas del host, los contenedores y SMART. | ✅ Implementado |
| Grafana | Muestra las métricas recogidas por Prometheus. | ✅ Implementado |
| Node Exporter | Aporta métricas del host. | ✅ Implementado |
| cAdvisor | Aporta métricas de los contenedores. | ✅ Implementado |
| smartctl-exporter | Aporta métricas SMART de los discos. | ✅ Implementado |
| Uptime Kuma | Comprueba la disponibilidad de los servicios. | ✅ Implementado |

La monitorización actual todavía tiene dos puntos abiertos:

- 🟡 **En proceso:** revisar las métricas de la interfaz física de red. Node Exporter se ejecuta en un contenedor con su propio espacio de red y no ve directamente la interfaz física del servidor. Por eso faltan algunas métricas de red del host.
- 🟡 **En proceso:** completar las alertas avanzadas y las notificaciones.

## Administración

### Portainer

Portainer se utiliza para administrar el entorno Docker. Complementa los archivos de Docker Compose, que siguen siendo la base para definir los servicios.

### Homepage

Homepage funciona como dashboard y punto de acceso visual a los servicios del homelab.

### Samba

Samba permite acceder a datos compartidos desde la red local. Está limitado a la interfaz de la LAN, no permite acceso invitado y utiliza SMB2 como versión mínima, con soporte para SMB3.

### Servicios auxiliares

- **docker-socket-proxy:** proporciona a vixi-status acceso controlado a la API de Docker sin conectarlo directamente al socket.
- **vixi-status:** reúne el estado del sistema, Docker, el almacenamiento y OpenClaw para que pueda consultarse desde un único servicio.

### Controles del sistema

- **UFW** está activo como firewall del servidor.
- **Fail2ban** protege el acceso SSH.
- **AppArmor** está activo y se aplica a los contenedores relevantes.

## Automatización y agentes

### OpenClaw, Vixi y Hermes

OpenClaw está instalado directamente sobre Ubuntu, no dentro de Docker. Vixi funciona como agente principal. Hermes ya existe como agente separado, pero su arquitectura de delegación, especialización y futuros subagentes sigue 🟡 **En proceso**. El Gateway se mantiene dentro de la red local y protegido por firewall.

El plugin de Codex está habilitado para integrar el trabajo sobre proyectos.

### Codex nativo

Codex CLI está instalado para el usuario principal. Trabaja directamente sobre proyectos y espacios de trabajo con los permisos de ese usuario; no está limitado al entorno aislado de Vixi Web.

### Vixi Web y Codex aislado

Vixi Web se ejecuta como servicio `systemd` mediante un usuario dedicado. Su entorno Codex solo puede escribir en los espacios de trabajo autorizados y permanece separado de Codex nativo.

Las pruebas realizadas confirman que este entorno puede escribir donde está autorizado, pero no sobre los datos generales, los datos persistentes de aplicaciones ni los metadatos internos de Git.

## En proceso y pendiente

| Estado | Elemento |
|---|---|
| 🟡 En proceso | Monitorización avanzada, revisión de métricas de red y mejora de alertas. |
| 🟡 En proceso | Migración desde la solución provisional de copias de seguridad. |
| 🟡 En proceso | Arquitectura de delegación, especialización y futuros subagentes de Hermes. |
| ⬜ Pendiente | Proxy inverso. |
| ⬜ Pendiente | HTTPS para la publicación del porfolio. |
| ⬜ Pendiente | Autenticación centralizada y claves de acceso (*passkeys*). |
| ⬜ Pendiente | Integración de OpenClaw con Telegram y, más adelante, WhatsApp. |
| ⬜ Pendiente | Prueba completa de restauración de copias de seguridad. |

## Política de actualizaciones

Watchtower actualiza automáticamente solo los servicios de monitorización que están autorizados para ello. No se aplica de forma general a todos los contenedores.

Los servicios críticos o sensibles se actualizan manualmente.
