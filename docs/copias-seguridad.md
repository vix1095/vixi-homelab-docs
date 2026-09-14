# Copias de seguridad

> **Estado general: ✅ Implementado**

Kopia se ejecuta en Docker y genera una copia automática diaria en un repositorio remoto privado de Cloudflare R2, con jurisdicción en la Unión Europea. Los datos se cifran antes de salir del servidor y Kopia aplica compresión, deduplicación y versionado.

La copia no se da por válida solo porque la tarea termine correctamente. También hay controles sobre el resultado y ya se ha probado una restauración real desde el repositorio remoto.

## Funcionamiento

Antes de cada ejecución se preparan copias consistentes de las bases de datos SQLite y una zona temporal con la configuración del sistema que debe conservarse. Después, el proceso principal ejecuta Kopia con bloqueo para evitar solapamientos, controla los errores y actualiza el registro y el estado del último backup.

Una comprobación local revisa la última ejecución correcta y detecta si la copia supera la antigüedad máxima admitida.

## Retención

La política conserva:

- las 7 últimas copias;
- 7 copias diarias;
- 4 copias semanales;
- 6 copias mensuales;
- 1 copia anual;
- ninguna copia horaria.

## Cobertura

| Elemento | Estado | Situación |
|---|---|---|
| Ejecución automática diaria | ✅ Implementado | Genera una copia cifrada y versionada fuera del servidor principal. |
| Configuraciones y stacks | ✅ Implementado | Incluye las definiciones y la configuración necesarias para reconstruir los servicios. |
| Datos persistentes de aplicaciones | ✅ Implementado | Cubre los datos relevantes de los servicios y sus copias consistentes de SQLite. |
| Scripts, espacios de trabajo y estados auxiliares | ✅ Implementado | Conserva los elementos necesarios para recuperar las automatizaciones y herramientas del entorno. |
| OpenClaw, Vixi y Hermes | ✅ Implementado | Sus datos necesarios forman parte de la cobertura definida. |
| Documentos del HDD | ✅ Implementado | El directorio `/data/documents` está incluido. |
| Registro, estado y control de errores | ✅ Implementado | Permite comprobar el resultado del proceso y detectar copias demasiado antiguas. |
| Restauración real desde el repositorio remoto | ✅ Implementado | Se ha recuperado información desde R2 y se ha verificado su integridad. |

## Qué queda fuera

Las películas, las series y las descargas se excluyen de forma deliberada. También queda fuera una copia antigua y redundante del porfolio. El backup se centra en los elementos necesarios para reconstruir el sistema, sus servicios y los documentos que sí requieren protección remota.

## Restauración validada

Se ha realizado una restauración real desde Cloudflare R2. El contenido recuperado se comparó mediante SHA-256 y las bases de datos SQLite pasaron una comprobación de integridad.

Existe además un procedimiento de recuperación documentado para reconstruir el entorno y restaurar los datos sin guardar secretos dentro de la propia documentación.

## En proceso

- 🟡 Corregir la integración de vixi-status, que todavía consulta la fuente del sistema anterior en lugar del estado real de Kopia.
- 🟡 Integrar el estado real de Kopia con la monitorización y las alertas centralizadas.
- 🟡 Retirar el sistema de copias anterior basado en SMB cuando se confirme que ya no aporta una copia útil. El sistema continúa presente como respaldo, pero su programación y actividad actual deben verificarse antes de decidir su retirada.
