# Almacenamiento

El almacenamiento se reparte entre un SSD de unos 223 GB y un HDD de 2 TB. El SSD concentra el sistema y la parte persistente de las aplicaciones. El HDD, montado en `/data`, se utiliza principalmente para multimedia, descargas y datos de gran tamaño.

| Unidad | Estado | Uso principal |
|---|---|---|
| SSD | ✅ Implementado | Ubuntu Server, LVM, stacks y datos persistentes de Docker. |
| HDD | ✅ Implementado | Multimedia, descargas y otros datos grandes. |
| Espacio restante del SSD | ⬜ Pendiente | Su uso todavía no está decidido. |

## SSD: sistema y aplicaciones

El SSD contiene Ubuntu Server y utiliza LVM. También aloja las definiciones de Docker Compose en `/opt/stacks` y los datos persistentes de los servicios en `/opt/appdata`.

No todo el espacio disponible está asignado a un uso definitivo. Prefiero mantener esa decisión como pendiente hasta tener claro qué necesita el sistema.

## HDD: multimedia y datos grandes

El HDD se monta en `/data` y guarda principalmente las bibliotecas multimedia, las descargas y otros datos de gran tamaño. Las películas y las series no forman parte de las copias de configuración.

## Criterio de separación

La idea es sencilla: el sistema, las definiciones de los stacks y la persistencia de las aplicaciones quedan en el SSD; el contenido multimedia, las descargas y los datos que ocupan más espacio van al HDD.

Esta separación evita mezclar las bibliotecas multimedia con el disco del sistema y mantiene más clara la relación entre Docker y sus datos. No significa que todos los datos persistentes estén en el HDD: `/opt/appdata` y `/opt/stacks` están en el SSD.

## Pendiente

- ⬜ Decidir cómo utilizar el espacio restante del SSD.
- 🟡 Rediseñar la estrategia de copias de seguridad. Su funcionamiento y alcance se documentarán en [Copias de seguridad](copias-seguridad.md).
