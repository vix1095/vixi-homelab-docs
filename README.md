# VIXI Homelab

Este repositorio recoge la documentación pública de mi homelab. Está montado sobre Ubuntu Server y contenedores, y reúne servicios de infraestructura, multimedia, monitorización y automatización. También incluye OpenClaw con Vixi, integración con Codex y un entorno aislado para automatizaciones con permisos limitados.

Aquí explico cómo está organizado y por qué tomé algunas decisiones técnicas, pero sin publicar direcciones de red, credenciales, secretos ni otros datos internos.

## Estado del proyecto

| Estado | Área | Situación |
|---|---|---|
| ✅ Implementado | Plataforma base | Servidor físico con Ubuntu Server 24.04 LTS, almacenamiento separado para el sistema y los datos, Docker y Docker Compose. |
| ✅ Implementado | Infraestructura | Pi-hole con DNS interno, WireGuard, Nginx Proxy Manager, Portainer, Uptime Kuma y actualizaciones automáticas solo para servicios seleccionados. |
| ✅ Implementado | Acceso web interno | Los servicios integrados usan nombres internos y HTTPS válido sin abrir nuevos puertos en el router. |
| ✅ Implementado | Multimedia | Gestión, descarga, organización y reproducción multimedia mediante un flujo automatizado. |
| ✅ Implementado | Monitorización | Prometheus y sus exportadores recogen métricas del host, los contenedores y el estado SMART. Grafana las muestra en paneles. |
| ✅ Implementado | Seguridad | Firewall, protección de SSH, AppArmor, acceso remoto privado y servicios de red local restringidos. |
| ✅ Implementado | Automatización asistida | OpenClaw con Vixi como agente principal. Codex nativo se ejecuta con los permisos del usuario principal, y Vixi Web tiene además un entorno Codex aislado limitado a espacios de trabajo autorizados. |
| ✅ Implementado | Documentación v1 | La primera versión pública está cerrada y revisada. Seguirá evolucionando junto al homelab. |
| 🟡 En proceso | Hermes | Ya existe como agente separado, pero su arquitectura de delegación, especialización y futuros subagentes sigue en desarrollo. |
| ✅ Implementado | Porfolio | Está desplegado en producción mediante Cloudflare Tunnel, con HTTPS obligatorio, TLS moderno y HSTS conservador. |
| ✅ Implementado | Copias de seguridad | Kopia genera copias cifradas y versionadas en almacenamiento remoto. La restauración real ya se ha validado. |
| 🟡 En proceso | Monitorización avanzada | Todavía tengo que revisar las métricas de red y completar las alertas y notificaciones. |

## Arquitectura general

El diagrama no incluye direcciones, subredes, dominios ni rutas reales.

```mermaid
flowchart LR
    remoto[Usuario remoto]
    vpn[WireGuard]
    host[Servidor Ubuntu]

    subgraph contenedores[Servicios en Docker]
        infraestructura[Infraestructura y administración]
        multimedia[Automatización multimedia]
        monitorizacion[Monitorización y métricas]
    end

    subgraph agentes[Automatización asistida]
        openclaw[OpenClaw y Vixi]
        hermes[Hermes · 🟡 En proceso]
        codex_nativo[Codex nativo]
        vixi_web[Vixi Web]
        codex_aislado[Codex aislado]
    end

    datos[(Almacenamiento de datos)]
    clientes[Clientes de la red local]
    espacios[Espacios de trabajo autorizados]

    remoto --> vpn --> host
    clientes --> host
    clientes --> infraestructura
    clientes --> multimedia
    host --> contenedores
    multimedia --> datos
    monitorizacion --> host
    monitorizacion --> contenedores
    host --> openclaw
    openclaw --> hermes
    host -->|Permisos del usuario principal| codex_nativo
    host --> vixi_web
    vixi_web -->|Usuario dedicado| codex_aislado
    codex_aislado -->|Acceso limitado| espacios
```

## Tecnologías utilizadas

- **Sistema y contenedores:** Ubuntu Server 24.04 LTS, Docker y Docker Compose.
- **Infraestructura:** Pi-hole, WireGuard, wg-easy, Nginx Proxy Manager, Portainer, Uptime Kuma y Watchtower.
- **Multimedia:** Plex, Sonarr, Radarr, Jackett, qBittorrent, Ruddarr y Cloudflare WARP.
- **Monitorización:** Prometheus, Grafana, Node Exporter, cAdvisor y smartctl-exporter.
- **Copias de seguridad:** Kopia y Cloudflare R2.
- **Publicación del porfolio:** Gunicorn, Cloudflare Tunnel y cloudflared.
- **Seguridad:** UFW, Fail2ban, AppArmor y Samba con acceso restringido a la red local.
- **Agentes y desarrollo:** OpenClaw, Vixi, Hermes y Codex.

## Decisiones técnicas destacadas

- **Contenedores como plataforma principal:** uso Docker para mantener los servicios separados, organizarlos mejor y simplificar el mantenimiento.
- **Acceso remoto privado:** utilizo una conexión privada para entrar desde fuera. Los servicios administrativos no están pensados para exponerse directamente a Internet.
- **Acceso web interno:** Pi-hole resuelve los nombres internos y Nginx Proxy Manager centraliza el acceso mediante HTTPS válido.
- **Publicación separada:** el porfolio es el único servicio público y utiliza Cloudflare Tunnel. Nginx Proxy Manager sigue reservado para el acceso interno.
- **Superficie pública acotada:** el acceso directo de producción desde la LAN ya está retirado. Los servicios internos y administrativos continúan fuera de Internet.
- **Actualizaciones selectivas:** automatizo las actualizaciones de algunos servicios de monitorización. Los componentes críticos o sensibles los actualizo manualmente.
- **Entorno de trabajo restringido:** Codex nativo se ejecuta con los permisos del usuario principal. Para Vixi Web mantengo otro entorno Codex aislado, ejecutado con un usuario dedicado, que solo puede trabajar sobre los espacios autorizados.
- **Documentación saneada:** cuando necesito mostrar una configuración de red, uso nombres genéricos como `SERVER_LAN_IP`, `LAN_SUBNET` y `VPN_SUBNET`.

## Documentación

La documentación v1 está cerrada y revisada. Está dividida en estos apartados y seguirá actualizándose cuando cambie el homelab:

- [Arquitectura](docs/arquitectura.md)
- [Servicios](docs/servicios.md)
- [Red](docs/red.md)
- [Seguridad](docs/seguridad.md)
- [Almacenamiento](docs/almacenamiento.md)
- [Multimedia](docs/multimedia.md)
- [Monitorización](docs/monitorizacion.md)
- [Copias de seguridad](docs/copias-seguridad.md)
- [OpenClaw y Vixi](docs/openclaw-vixi.md)
- [Integración con el porfolio](docs/integracion-portfolio.md)

## Hoja de ruta

### ✅ Implementado

- Plataforma base con Ubuntu Server, Docker y Docker Compose.
- Servicios de infraestructura, multimedia y monitorización descritos en este README.
- Acceso remoto privado y controles básicos de seguridad.
- OpenClaw con Vixi, Codex nativo con los permisos del usuario principal y un entorno Codex aislado para Vixi Web.
- Copias de seguridad cifradas y versionadas con una restauración real validada.
- DNS interno, proxy inverso y HTTPS para los servicios web integrados en la red local.
- Porfolio publicado en [vixstack.es](https://vixstack.es) mediante Cloudflare Tunnel, sin *port forwarding* web.
- Primera versión de la documentación pública del homelab.

### 🟡 En proceso

- Retirada progresiva del sistema de copias anterior basado en SMB.
- Integración del estado de Kopia con las alertas centralizadas.
- Monitorización avanzada, alertas y revisión de métricas de red.
- Arquitectura de delegación, especialización y futuros subagentes de Hermes.
- Revisión y rotación periódica de las credenciales de infraestructura utilizadas con Cloudflare.

### ⬜ Pendiente

- Autenticación centralizada y claves de acceso (*passkeys*).
- Integración de OpenClaw con Telegram y, posteriormente, WhatsApp.

## Criterios de publicación

Quiero explicar qué he montado y por qué, pero sin dar información que permita acceder al entorno. Por eso no publico credenciales, secretos, direcciones reales, subredes exactas, identificadores de sesión, contenido privado ni ubicaciones de archivos sensibles.
