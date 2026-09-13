# Seguridad

La seguridad del homelab se apoya en varias capas sencillas: limitar la exposición, controlar el acceso al servidor, aislar los servicios y separar los permisos de las automatizaciones. El porfolio es público, pero los servicios internos y administrativos se mantienen dentro de la red privada.

## Enfoque

La LAN es el entorno de acceso habitual. Cuando necesito entrar desde fuera utilizo WireGuard como acceso remoto privado. UFW filtra el tráfico que llega al servidor y cada servicio mantiene el alcance mínimo que necesita.

## Capas actuales

| Capa | Estado | Función |
|---|---|---|
| UFW | ✅ Implementado | Controla el acceso de red al servidor. |
| Fail2ban | ✅ Implementado | Protege SSH frente a intentos repetidos de acceso. |
| Configuración de SSH | ✅ Implementado | Deshabilita el acceso root y la autenticación interactiva por teclado. Admite claves públicas y mantiene la contraseña como respaldo. |
| AppArmor | ✅ Implementado | Añade aislamiento a los contenedores Docker relevantes. |
| WireGuard | ✅ Implementado | Proporciona acceso remoto privado al homelab. |
| Samba | ✅ Implementado | Queda limitado a la red local y no permite acceso invitado. |
| Copias de seguridad | ✅ Implementado | Kopia cifra los datos antes de enviarlos al almacenamiento remoto y conserva versiones recuperables. |
| HTTPS interno | ✅ Implementado | Nginx Proxy Manager protege con HTTPS los servicios web integrados dentro de la red privada. |
| Publicación del porfolio | ✅ Implementado | Cloudflare Tunnel publica únicamente el porfolio sin abrir puertos web en el router. El acceso fuerza HTTPS y utiliza TLS moderno. |

SSH no funciona exclusivamente con claves. La autenticación mediante clave pública está disponible, pero la autenticación por contraseña sigue habilitada como mecanismo de respaldo. No se retirará hasta comprobar por completo los accesos alternativos necesarios.

## Acceso remoto

WireGuard es el camino previsto para entrar desde fuera sin exponer directamente los servicios administrativos. El acceso concreto a cada servicio debe validarse por separado; no se da por hecho que todos estén disponibles a través de la VPN.

## Aislamiento y permisos

Docker separa los servicios en contenedores y AppArmor añade una capa de control sobre los contenedores relevantes.

Las herramientas automatizadas tampoco comparten todas el mismo nivel de acceso. Codex nativo trabaja con los permisos del usuario principal. Vixi Web utiliza un usuario dedicado y un entorno Codex aislado que solo puede escribir en los espacios de trabajo autorizados. Fuera de ese ámbito, la escritura está bloqueada.

## Gestión de secretos

Las contraseñas, tokens, claves, cookies, credenciales y archivos de entorno quedan fuera de la documentación y del contenido público del repositorio. Tampoco se publican sus ubicaciones ni detalles que permitan reconstruir el acceso al servidor.

Los ejemplos de configuración, cuando hagan falta, deben utilizar valores genéricos en lugar de datos reales.

## HTTPS interno

Nginx Proxy Manager gestiona el acceso HTTPS a los servicios web internos y fuerza la redirección desde HTTP. Los certificados wildcard se obtienen con Let's Encrypt mediante el DNS Challenge de Cloudflare. Así la validación ACME no necesita abrir puertos web en el router.

Las credenciales necesarias para la validación no forman parte de esta documentación. HTTP/2 está activo; HSTS interno no está activado por ahora.

Esta capa mejora el acceso dentro de la LAN, pero no publica los servicios administrativos en Internet. Algunos servicios internos mantienen temporalmente sus puertos directos como respaldo y su retirada sigue 🟡 **En proceso**.

## Porfolio público

El porfolio se publica mediante Cloudflare Tunnel. cloudflared se ejecuta en un contenedor aislado y conecta únicamente con el servicio que debe ser público, sin exponer Nginx Proxy Manager ni abrir puertos web en el router.

El contenedor del porfolio aplica mínimo privilegio, sistema de archivos de solo lectura, límites de recursos y un healthcheck propio. El resto de servicios internos y administrativos continúa fuera del recorrido público.

El acceso HTTP redirige de forma permanente a HTTPS. La versión mínima admitida es TLS 1.2 y TLS 1.3 está operativo, por lo que la publicación no depende de versiones antiguas del protocolo.

HSTS está activo de forma conservadora solo para el dominio público: utiliza un periodo inicial limitado, no incluye todos los subdominios y no usa `preload`. Es una decisión separada del HSTS interno de Nginx Proxy Manager, que sigue desactivado.

Cloudflare añade una capa básica de protección y Bot Fight Mode está activo. No lo considero una solución completa por sí sola. El propio porfolio entrega además cabeceras para limitar la carga de contenido, los permisos del navegador, la información de referencia y la interpretación incorrecta de tipos de contenido.

El antiguo acceso directo del porfolio de producción desde la LAN ya se ha retirado y se ha comprobado que no responde. El entorno de producción queda accesible por las redes Docker necesarias para el proxy interno y Cloudflare Tunnel, sin una exposición directa adicional.

## Actualizaciones

Watchtower actualiza automáticamente solo los servicios de monitorización autorizados. Los servicios críticos o sensibles se actualizan manualmente.

## En proceso y pendiente

| Estado | Mejora |
|---|---|
| 🟡 En proceso | Consolidar y rotar las credenciales de infraestructura utilizadas con Cloudflare. |
| 🟡 En proceso | Valorar una versión explícita de cloudflared en lugar de una etiqueta flotante. Es una mejora de baja prioridad. |
| ⬜ Pendiente | Añadir autenticación centralizada y claves de acceso (*passkeys*) donde sean necesarias. |
