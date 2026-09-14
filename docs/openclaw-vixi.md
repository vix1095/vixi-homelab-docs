# OpenClaw y Vixi

OpenClaw está instalado directamente sobre Ubuntu, no dentro de Docker. Vixi funciona como agente principal y coordina el uso de herramientas y agentes dentro del homelab.

El Gateway se mantiene en acceso privado, está accesible desde la LAN y no se expone directamente a Internet. WireGuard es el mecanismo general de acceso remoto al homelab y el acceso concreto a cada servicio se valida por separado.

## Componentes

| Componente | Estado | Función y alcance |
|---|---|---|
| OpenClaw y Vixi | ✅ Implementado | OpenClaw proporciona la base y Vixi actúa como agente principal. |
| Codex nativo | ✅ Implementado | Trabaja directamente sobre proyectos con los permisos del usuario principal. |
| Vixi Web y Codex aislado | ✅ Implementado | Utiliza un usuario dedicado y solo puede trabajar sobre espacios autorizados. |
| Telegram | ✅ Implementado | Funciona como canal externo con control de acceso para mensajes privados y grupos deshabilitados. |
| Voz local | ✅ Implementado | Whisper transcribe la entrada y Supertonic genera la respuesta hablada. |
| Memoria | ✅ Implementado | Utiliza embeddings locales y búsqueda textual y vectorial. |
| Hermes | 🟡 En proceso | Existe como agente separado, pero su arquitectura avanzada sigue incompleta y su desarrollo está pausado. |

## Vixi como agente principal

Vixi es el punto de entrada para coordinar tareas dentro de OpenClaw. Puede apoyarse en Codex para trabajo sobre proyectos y en Hermes para delegar tareas concretas, pero eso no lo convierte en un sistema autónomo sin límites.

Las acciones sensibles requieren aprobación explícita. La automatización queda limitada por los permisos de cada entorno y por las reglas de seguridad definidas para la tarea.

## Dos entornos de Codex

Codex nativo se ejecuta con los permisos del usuario principal. Se utiliza para trabajar directamente sobre proyectos y espacios de trabajo, por lo que no está limitado al aislamiento de Vixi Web.

Vixi Web utiliza un usuario dedicado y un entorno Codex separado. Ese entorno tiene permisos más limitados y solo puede trabajar sobre espacios autorizados. La separación es intencionada: una automatización restringida no necesita el mismo acceso que una sesión nativa controlada por el usuario principal.

## Hermes

Hermes existe como agente separado dentro de la arquitectura de OpenClaw. Su papel es apoyar la delegación y la especialización, mientras Vixi mantiene la coordinación principal.

La base está creada, pero la arquitectura avanzada de delegación y especialistas no está terminada. Su desarrollo está pausado mientras se consolida OpenClaw/Vixi.

## Telegram y voz local

Telegram está operativo como canal externo de Vixi. Los mensajes privados tienen control de acceso y los grupos permanecen deshabilitados.

La interacción por voz también funciona en local:

- una entrada de voz se transcribe con Whisper y recibe una respuesta de voz generada con Supertonic;
- una entrada escrita recibe una respuesta escrita.

Tanto la transcripción como la síntesis se ejecutan localmente. La configuración interna y los datos de acceso no forman parte de esta documentación.

## Memoria, Knowledge Base y skills

El sistema base de memoria está ✅ **implementado**. Utiliza embeddings locales y combina búsqueda textual y vectorial, sin recurrir a un fallback remoto para generar esos embeddings.

La memoria y la Knowledge Base privada cumplen funciones complementarias. La memoria conserva contexto útil entre tareas y la KB reúne contexto técnico y operativo. Este repositorio público contiene únicamente la versión saneada del proyecto, sin copiar la estructura ni el contenido privado.

Las skills reúnen procedimientos reutilizables y siguen evolucionando cuando aparece una necesidad real.

Vixi dispone de una skill específica para revisar qué documentos quedan afectados después de un cambio validado, mantener los criterios de privacidad y preparar la actualización pública. La publicación final sigue requiriendo revisión y aprobación.

## Automatizaciones

Vixi utiliza tareas periódicas para mantener el contexto y la memoria, comprobar el estado del entorno y revisar las skills. Su evolución continúa, pero la base ya está operativa.

## Gestión de secretos

La gestión de secretos está integrada mediante SecretRefs y se mantiene fuera de la documentación pública. Una revisión puntual no detectó secretos estáticos ni problemas con esas referencias, aunque esto no sustituye futuras revisiones.

Queda mantenimiento menor relacionado con el versionado de plugins. No se publican nombres, configuración interna ni referencias concretas.

## Evolución pendiente

- 🟡 **En proceso:** mantener y mejorar las skills cuando aporten valor.
- 🟡 **En proceso:** ampliar y revisar las automatizaciones.
- 🟡 **En proceso:** retomar la evolución de Hermes cuando termine la consolidación de OpenClaw/Vixi.
