# OpenClaw y Vixi

OpenClaw está instalado directamente sobre Ubuntu, no dentro de Docker. Vixi funciona como agente principal y coordina el uso de herramientas y agentes dentro del homelab.

El Gateway se mantiene en acceso privado, está accesible desde la LAN y no se expone directamente a Internet. WireGuard es el mecanismo general de acceso remoto al homelab y el acceso concreto a cada servicio se valida por separado.

## Componentes

| Componente | Estado | Función y alcance |
|---|---|---|
| OpenClaw y Vixi | ✅ Implementado | OpenClaw proporciona la base y Vixi actúa como agente principal. |
| Codex nativo | ✅ Implementado | Trabaja directamente sobre proyectos con los permisos del usuario principal. |
| Vixi Web y Codex aislado | ✅ Implementado | Utiliza un usuario dedicado y solo puede trabajar sobre espacios autorizados. |
| Hermes | 🟡 En proceso | Ya existe como agente separado, pero su arquitectura de delegación y especialización sigue en desarrollo. |

## Vixi como agente principal

Vixi es el punto de entrada para coordinar tareas dentro de OpenClaw. Puede apoyarse en Codex para trabajo sobre proyectos y en Hermes para delegar tareas concretas, pero eso no lo convierte en un sistema autónomo sin límites.

Las acciones sensibles requieren aprobación explícita. La automatización queda limitada por los permisos de cada entorno y por las reglas de seguridad definidas para la tarea.

## Dos entornos de Codex

Codex nativo se ejecuta con los permisos del usuario principal. Se utiliza para trabajar directamente sobre proyectos y espacios de trabajo, por lo que no está limitado al aislamiento de Vixi Web.

Vixi Web utiliza un usuario dedicado y un entorno Codex separado. Ese entorno tiene permisos más limitados y solo puede trabajar sobre espacios autorizados. La separación es intencionada: una automatización restringida no necesita el mismo acceso que una sesión nativa controlada por el usuario principal.

## Hermes

Hermes existe como agente separado dentro de la arquitectura de OpenClaw. Su papel es apoyar la delegación y la especialización, mientras Vixi mantiene la coordinación principal.

La base está creada, pero la arquitectura de Hermes y la incorporación de agentes especializados siguen en desarrollo y se tratan como una evolución independiente.

## Memoria, skills y automatizaciones

La memoria sirve para conservar contexto útil entre tareas y las skills reúnen procedimientos reutilizables. Tanto esta parte como las automatizaciones siguen evolucionando.

Vixi dispone de una skill específica para revisar qué documentos quedan afectados después de un cambio validado, mantener los criterios de privacidad y preparar la actualización pública. La publicación final sigue requiriendo revisión y aprobación.

Los secretos deben permanecer fuera de la documentación y de los espacios públicos. El uso de SecretRefs todavía está pendiente; esta página solo describe el criterio general, no su configuración interna.

## Evolución pendiente

- 🟡 **En proceso:** seguir mejorando la memoria y las skills.
- 🟡 **En proceso:** ampliar y revisar las automatizaciones.
- ⬜ **Pendiente:** incorporar SecretRefs para la gestión de secretos.
- ⬜ **Pendiente:** añadir Telegram como canal externo.
- 🟡 **En proceso:** continuar la evolución de Hermes y de los agentes especializados.
