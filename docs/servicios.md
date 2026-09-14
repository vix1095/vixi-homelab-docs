# Servicios

La mayoría de servicios del homelab se ejecutan con Docker y Docker Compose. OpenClaw está instalado directamente sobre Ubuntu y Codex nativo trabaja sobre el sistema con los permisos de su usuario. Vixi Web usa además un entorno separado con permisos limitados.

## Resumen

| Área | Componentes | Estado | Función |
|---|---|---|---|
| Infraestructura | Pi-hole, WireGuard, wg-easy, Nginx Proxy Manager y Uptime Kuma | ✅ Implementado | DNS interno, acceso remoto privado, proxy inverso y comprobación de disponibilidad. |
| Multimedia | Ruddarr, Radarr, Sonarr, Jackett, qBittorrent, Cloudflare WARP y Plex | ✅ Implementado | Gestionar las peticiones, buscar contenido, descargarlo, organizarlo y reproducirlo. |
| Monitorización | Prometheus, Grafana, Node Exporter, cAdvisor y smartctl-exporter | ✅ Implementado | Recoger y mostrar métricas del host, los contenedores y los discos. |
| Copias de seguridad | Kopia y Cloudflare R2 | ✅ Implementado | Generar copias cifradas, versionadas y deduplicadas en almacenamiento remoto. |
| Publicación web | Porfolio, Gunicorn y cloudflared | ✅ Implementado | Publicar el porfolio mediante Cloudflare Tunnel sin abrir puertos web en el router. |
| Administración | Portainer, Homepage, Samba y Watchtower | ✅ Implementado | Administrar contenedores, acceder a los servicios, compartir datos en la LAN y aplicar actualizaciones autorizadas. |
| Servicios auxiliares | docker-socket-proxy y vixi-status | ✅ Implementado | Consultar el estado de Docker y del homelab sin dar acceso directo al socket. |
| Seguridad del sistema | UFW, Fail2ban y AppArmor | ✅ Implementado | Controlar accesos y aplicar protección al host y a los contenedores relevantes. |
| Automatización y agentes | OpenClaw, Vixi, Codex y Vixi Web | ✅ Implementado | Coordinar tareas y trabajar sobre proyectos con distintos niveles de permisos. |
| Delegación y especialización | Hermes | 🟡 En proceso | El agente ya existe, pero su arquitectura y sus futuros subagentes siguen en desarrollo. |

## Infraestructura

### Pi-hole

Pi-hole forma parte de los servicios base de la red local. Se encarga del filtrado DNS y también resuelve los nombres internos de los servicios para los clientes que lo utilizan.

### Nginx Proxy Manager

Nginx Proxy Manager funciona como proxy inverso interno. Centraliza el acceso a las interfaces web, fuerza HTTP a HTTPS y utiliza certificados válidos emitidos mediante Let's Encrypt y el DNS Challenge de Cloudflare. Este método permite validar y renovar los certificados sin abrir puertos web en el router.

Los servicios integrados comparten la red Docker `proxy` y mantienen sus redes originales cuando las necesitan. Otros conservan su arquitectura específica y Nginx Proxy Manager llega a ellos sin conectarlos directamente a esa red.

El porfolio también puede gestionarse desde esta capa interna, pero su publicación en Internet no pasa por Nginx Proxy Manager.

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

## Porfolio y Cloudflare Tunnel

El porfolio está desplegado en Docker y utiliza Gunicorn. El contenedor se ejecuta sin privilegios de root, con el sistema de archivos en modo de solo lectura, almacenamiento temporal separado, capabilities innecesarias eliminadas, `no-new-privileges`, límites de recursos y un healthcheck propio.

cloudflared también se ejecuta en Docker con aislamiento y mínimo privilegio. No publica puertos en el host y conecta Cloudflare Tunnel con el porfolio mediante la red Docker compartida. Esta es la ruta pública definitiva; Nginx Proxy Manager continúa gestionando el acceso interno.

## Monitorización

Prometheus centraliza las métricas que producen los exportadores. Grafana se utiliza para visualizarlas y Uptime Kuma comprueba la disponibilidad de los servicios.

| Componente | Qué aporta | Estado |
|---|---|---|
| Prometheus | Recoge métricas del host, los contenedores y SMART. | ✅ Implementado |
| Grafana | Muestra las métricas recogidas por Prometheus. | ✅ Implementado |
| Node Exporter | Está operativo, pero no recoge las métricas de la interfaz física del servidor. | 🟡 En proceso |
| cAdvisor | Aporta métricas de los contenedores. | ✅ Implementado |
| smartctl-exporter | Aporta métricas SMART de los discos. | ✅ Implementado |
| Uptime Kuma | Comprueba la disponibilidad de los servicios. | ✅ Implementado |

La base de monitorización funciona, incluido el estado SMART principal de los discos. Quedan mejoras en las métricas de red, las alertas y notificaciones, los paneles y la cobertura de Docker y SMART. El detalle está en [Monitorización](monitorizacion.md).

## Copias de seguridad

Kopia se ejecuta en Docker y guarda copias cifradas y versionadas en un repositorio remoto privado. El proceso incluye la preparación consistente de las bases de datos SQLite, controles de error y comprobaciones sobre el estado de la última ejecución. Ya se ha validado una restauración real desde el almacenamiento remoto.

La retirada del sistema de copias anterior basado en SMB y la integración con las alertas centralizadas siguen 🟡 **En proceso**. El detalle de la cobertura, la retención y las exclusiones está en [Copias de seguridad](copias-seguridad.md).

## Administración

### Portainer

Portainer se utiliza para administrar el entorno Docker. Complementa los archivos de Docker Compose, que siguen siendo la base para definir los servicios.

### Homepage

Homepage funciona como dashboard y punto de acceso visual a los servicios del homelab.

### Samba

Samba permite acceder a datos compartidos desde la red local. Está limitado a la interfaz de la LAN, no permite acceso invitado y utiliza SMB2 como versión mínima, con soporte para SMB3.

### Servicios auxiliares

- **docker-socket-proxy:** proporciona a vixi-status acceso controlado a la API de Docker sin conectarlo directamente al socket.
- **vixi-status:** reúne el estado del sistema, Docker, el almacenamiento y OpenClaw para que pueda consultarse desde un único servicio. Su integración con el estado real de Kopia sigue pendiente.

### Controles del sistema

- **UFW** está activo como firewall del servidor.
- **Fail2ban** protege el acceso SSH.
- **AppArmor** está activo y se aplica a los contenedores relevantes.

## Automatización y agentes

### OpenClaw, Vixi y Hermes

OpenClaw está instalado directamente sobre Ubuntu, no dentro de Docker. Vixi funciona como agente principal y Telegram ya está operativo como canal externo. Hermes existe como agente separado, pero su arquitectura avanzada sigue incompleta y su desarrollo está pausado mientras se consolida OpenClaw/Vixi. El Gateway se mantiene dentro de la red local y protegido por firewall.

El plugin de Codex está habilitado para integrar el trabajo sobre proyectos.

### Codex nativo

Codex CLI está instalado para el usuario principal. Trabaja directamente sobre proyectos y espacios de trabajo con los permisos de ese usuario; no está limitado al entorno aislado de Vixi Web.

### Vixi Web y Codex aislado

Vixi Web se ejecuta como servicio `systemd` mediante un usuario dedicado. Su entorno Codex solo puede escribir en los espacios de trabajo autorizados y permanece separado de Codex nativo.

Las pruebas realizadas confirman que este entorno puede escribir donde está autorizado, pero no sobre los datos generales, los datos persistentes de aplicaciones ni los metadatos internos de Git.

## En proceso y pendiente

| Estado | Elemento |
|---|---|
| 🟡 En proceso | Monitorización avanzada: métricas de red, alertas, notificaciones, paneles y revisión de cobertura. |
| 🟡 En proceso | Retirada progresiva del sistema de copias anterior basado en SMB. |
| 🟡 En proceso | Integración de vixi-status con el estado real de Kopia y con las alertas centralizadas. |
| 🟡 En proceso | Arquitectura avanzada de Hermes, actualmente pausada mientras se consolida OpenClaw/Vixi. |
| 🟡 En proceso | Retirada progresiva de los puertos web directos de otros servicios internos. |
| ⬜ Pendiente | Desplegar Authentik como próximo paso para centralizar la autenticación. |

## Política de actualizaciones

Watchtower actualiza automáticamente Grafana, Prometheus, Node Exporter y Uptime Kuma. No se aplica de forma general a todos los contenedores.

Plex, Pi-hole, WireGuard y Portainer se actualizan manualmente. Esta política evita aplicar cambios automáticos indiscriminados a componentes con mayor impacto.
