# Copias de seguridad

> **Estado general: 🟡 En proceso**

Existe una copia automática diaria que se guarda fuera del servidor principal. Es una base funcional, pero sigue siendo provisional: tiene limitaciones conocidas y todavía no hay una restauración completa validada.

Que la tarea termine no basta para considerar que el backup es correcto. La referencia final será poder restaurarlo y comprobar que los datos necesarios vuelven a funcionar.

## Cobertura actual

| Elemento | Estado | Situación |
|---|---|---|
| Ejecución automática diaria | ✅ Implementado | Genera una copia fuera del servidor principal. |
| Configuraciones y stacks | ✅ Implementado | Forman parte de la copia actual. |
| Datos persistentes relevantes | ✅ Implementado | Se incluyen dentro de la cobertura definida actualmente. |
| Inventario del sistema | ✅ Implementado | Se conserva como apoyo para reconstruir el entorno. |
| Registro de ejecución y estado | ✅ Implementado | Permite consultar el resultado general del proceso. |
| Estrategia definitiva | 🟡 En proceso | Se está rediseñando la cobertura y el método de almacenamiento. |
| Restauración completa validada | ⬜ Pendiente | Todavía no se ha probado de principio a fin. |

## Qué queda fuera

Las películas y las series se excluyen de forma deliberada de este backup de configuración. La copia se centra en los elementos necesarios para reconstruir el sistema y sus servicios, no en las bibliotecas multimedia.

## Limitaciones conocidas

- Funciona principalmente como copia o espejo, no como un sistema completo de backup versionado.
- La cobertura actual todavía debe revisarse.
- Un resultado general correcto puede ocultar fallos en partes concretas del proceso.
- No todas las aplicaciones tienen garantizada una copia consistente. Algunas pueden necesitar snapshots o exportaciones específicas.
- No existe todavía una restauración completa validada.

## Estrategia futura

El rediseño busca una solución cifrada, versionada y con restauraciones probadas. También deberá mejorar la detección de errores y definir qué aplicaciones necesitan un tratamiento específico para mantener la consistencia de sus datos.

Se está valorando utilizar almacenamiento remoto o cloud con cifrado del lado cliente, pero la solución final todavía no está elegida.

## Pendiente

- 🟡 **En proceso:** revisar la cobertura y diseñar la estrategia definitiva.
- ⬜ **Pendiente:** incorporar cifrado y versionado adecuados.
- ⬜ **Pendiente:** definir copias consistentes para las aplicaciones que lo necesiten.
- ⬜ **Pendiente:** validar una restauración completa antes de dar el sistema por fiable.
