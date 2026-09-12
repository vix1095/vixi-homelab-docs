# Monitorización

La monitorización combina métricas del sistema con comprobaciones de disponibilidad. Prometheus recibe los datos de los exportadores y Grafana los muestra en paneles. Uptime Kuma cubre otra parte: comprobar si los servicios responden.

La base está funcionando, pero todavía faltan algunas métricas de red y completar las alertas avanzadas.

## Componentes

| Herramienta | Función | Estado |
|---|---|---|
| Prometheus | Recopila las métricas de los exportadores principales. | ✅ Implementado |
| Grafana | Muestra las métricas recogidas por Prometheus. | ✅ Implementado |
| Node Exporter | Aporta métricas del host. | ✅ Implementado |
| cAdvisor | Aporta métricas de los contenedores. | ✅ Implementado |
| smartctl-exporter | Expone el estado y las métricas SMART de los discos. | ✅ Implementado |
| Uptime Kuma | Comprueba la disponibilidad de los servicios. | ✅ Implementado |

## Cómo trabajan juntas

Node Exporter, cAdvisor y smartctl-exporter cubren tres capas distintas: host, contenedores y discos. Prometheus recoge esas métricas correctamente y Grafana permite consultarlas desde un mismo sitio.

Uptime Kuma funciona de forma separada. No sustituye las métricas del sistema: sirve para detectar si un servicio responde o deja de estar disponible.

## Limitación actual

Node Exporter se ejecuta en un contenedor con su propio espacio de red y no ve directamente la interfaz física del servidor. Por eso faltan algunas métricas de red del host.

El resto de métricas principales y la información SMART de los discos ya llegan a Prometheus.

## Mejoras pendientes

- 🟡 **En proceso:** revisar cómo recoger correctamente las métricas de la interfaz física del servidor.
- 🟡 **En proceso:** completar las alertas avanzadas y las notificaciones.
