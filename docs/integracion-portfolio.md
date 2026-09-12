# Integración con el porfolio

> **Estado general: 🟡 En proceso**

El homelab será uno de los proyectos principales de mi porfolio. La idea es enseñar qué he montado, las decisiones que he tomado y cómo ha ido evolucionando, sin presentarlo como una infraestructura empresarial ni publicar detalles internos que no aportan nada.

El porfolio web y este repositorio son proyectos separados. La web presentará el caso de forma resumida y la documentación permitirá profundizar en la parte técnica.

## Qué mostrará el porfolio

La presentación se centrará en:

- el objetivo del homelab;
- una visión general de la arquitectura;
- las tecnologías principales;
- decisiones técnicas y motivos;
- problemas encontrados, aprendizajes y mejoras pendientes;
- enlaces a la documentación pública que aporte contexto.

También podrán incluirse diagramas o capturas cuando ayuden a entender el proyecto y estén revisados para uso público.

## Relación con la documentación

El porfolio debe poder leerse rápido. No duplicará toda la documentación: resumirá el proyecto y enlazará desde cada parte relevante a los documentos de este repositorio en GitHub.

Mantener separados el código de la web y la documentación permite actualizar cada parte sin mezclar el desarrollo del porfolio con el contenido técnico del homelab.

## Criterio de publicación y privacidad

Solo se publicará información que ayude a explicar la arquitectura, las decisiones o el aprendizaje. Quedan fuera las direcciones de red, puertos, credenciales, secretos, reglas internas de acceso y cualquier captura que revele datos sensibles.

Los diagramas y ejemplos deberán estar saneados. Si un detalle permite localizar, reconstruir o facilitar el acceso al entorno, no tiene sitio ni en el porfolio ni en la documentación pública.

## Estado actual

| Elemento | Estado | Situación |
|---|---|---|
| Separación de proyectos | ✅ Implementado | El porfolio y la documentación se mantienen en proyectos distintos. |
| Documentación pública v1 | ✅ Implementado | La primera versión está cerrada y sirve como base técnica del porfolio. |
| Porfolio web | 🟡 En proceso | Se desarrolla y revisa fuera de este repositorio. |
| Integración del homelab | 🟡 En proceso | Falta cerrar el resumen y los enlaces a la documentación seleccionada. |
| Publicación en `vixstack.es` | ⬜ Pendiente | Es el dominio previsto, pero la publicación todavía no está confirmada. |

## Actualización futura

El contenido deberá revisarse a medida que cambien la arquitectura y el estado del proyecto. El porfolio mantendrá el resumen y esta documentación recogerá el detalle confirmado, diferenciando siempre lo implementado de lo que sigue en proceso o pendiente.

El lanzamiento del porfolio y la publicación del dominio se gestionan fuera de este repositorio.
