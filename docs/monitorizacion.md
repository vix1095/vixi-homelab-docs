# Monitorización

La monitorización combina métricas del sistema con comprobaciones de disponibilidad. Prometheus recibe los datos de los exportadores y Grafana los muestra en paneles. Uptime Kuma cubre otra parte: comprobar si los servicios responden.

La base está funcionando y SMART ya aporta la información principal de los discos. Todavía quedan mejoras de cobertura, visualización y alertas.

## Componentes

| Herramienta | Función | Estado |
|---|---|---|
| Prometheus | Recopila las métricas de los exportadores principales. | ✅ Implementado |
| Grafana | Muestra las métricas recogidas por Prometheus. | ✅ Implementado |
| Node Exporter | Está operativo, pero no ve la interfaz física del servidor. | 🟡 En proceso |
| cAdvisor | Aporta métricas de los contenedores. | ✅ Implementado |
| smartctl-exporter | Expone el estado y las métricas SMART de los discos. | ✅ Implementado |
| Uptime Kuma | Comprueba la disponibilidad de los servicios. | ✅ Implementado |

## Cómo trabajan juntas

Node Exporter, cAdvisor y smartctl-exporter cubren tres capas distintas: host, contenedores y discos. Prometheus recibe correctamente los targets configurados y Grafana permite consultar sus métricas desde un mismo sitio.

Uptime Kuma funciona de forma separada. No sustituye las métricas del sistema: sirve para detectar si un servicio responde o deja de estar disponible.

## Limitación actual

Node Exporter se ejecuta en un contenedor con su propio espacio de red y no ve directamente la interfaz física del servidor. Por eso faltan algunas métricas de red del host.

El resto de métricas principales y la información SMART de los discos ya llegan a Prometheus.

vixi-status todavía utiliza la fuente del sistema de backup anterior. Por eso puede mostrar un aviso de copia desactualizada aunque Kopia haya terminado correctamente. Kopia funciona; lo pendiente es corregir esta integración.

SMART también genera un aviso recurrente del SSD que todavía necesita diagnóstico. No se considera un fallo crítico mientras no se confirme su causa.

Prometheus aún no tiene reglas de alerta cargadas. Las alertas centralizadas y sus notificaciones siguen pendientes.

## Mejoras pendientes

- 🟡 **En proceso:** revisar cómo recoger correctamente las métricas de la interfaz física del servidor.
- 🟡 **En proceso:** conectar vixi-status con el estado real de Kopia.
- 🟡 **En proceso:** revisar el aviso SMART recurrente del SSD.
- ⬜ **Pendiente:** completar las alertas avanzadas.
- ⬜ **Pendiente:** completar las notificaciones.
- ⬜ **Pendiente:** completar el canal de notificación de alertas SMART.
- ⬜ **Pendiente:** mejorar los paneles de Grafana.
- ⬜ **Pendiente:** revisar la cobertura de las métricas de Docker.
- ⬜ **Pendiente:** ampliar y revisar las temperaturas y las métricas SMART.
- ⬜ **Pendiente:** definir mejor qué debe monitorizar Prometheus y qué comprobaciones corresponden a Uptime Kuma.
