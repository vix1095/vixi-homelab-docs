# Seguridad

La seguridad del homelab se apoya en varias capas sencillas: limitar la exposición, controlar el acceso al servidor, aislar los servicios y separar los permisos de las automatizaciones. Los servicios administrativos se mantienen dentro de la red privada y no se publican directamente en Internet.

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

SSH no funciona exclusivamente con claves. La autenticación mediante clave pública está disponible, pero la autenticación por contraseña sigue habilitada como mecanismo de respaldo. No se retirará hasta comprobar por completo los accesos alternativos necesarios.

## Acceso remoto

WireGuard es el camino previsto para entrar desde fuera sin exponer directamente los servicios administrativos. El acceso concreto a cada servicio debe validarse por separado; no se da por hecho que todos estén disponibles a través de la VPN.

## Aislamiento y permisos

Docker separa los servicios en contenedores y AppArmor añade una capa de control sobre los contenedores relevantes.

Las herramientas automatizadas tampoco comparten todas el mismo nivel de acceso. Codex nativo trabaja con los permisos del usuario principal. Vixi Web utiliza un usuario dedicado y un entorno Codex aislado que solo puede escribir en los espacios de trabajo autorizados. Fuera de ese ámbito, la escritura está bloqueada.

## Gestión de secretos

Las contraseñas, tokens, claves, cookies, credenciales y archivos de entorno quedan fuera de la documentación y del contenido público del repositorio. Tampoco se publican sus ubicaciones ni detalles que permitan reconstruir el acceso al servidor.

Los ejemplos de configuración, cuando hagan falta, deben utilizar valores genéricos en lugar de datos reales.

## Actualizaciones

Watchtower actualiza automáticamente solo los servicios de monitorización autorizados. Los servicios críticos o sensibles se actualizan manualmente.

## Pendiente

| Estado | Mejora |
|---|---|
| ⬜ Pendiente | Añadir autenticación centralizada y claves de acceso (*passkeys*) donde sean necesarias. |
| ⬜ Pendiente | Incorporar proxy inverso y HTTPS antes de publicar los servicios que se decidan exponer. |
