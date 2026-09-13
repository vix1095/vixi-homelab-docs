# Almacenamiento

El almacenamiento se reparte entre un SSD de unos 223 GB y un HDD de 2 TB. El SSD concentra el sistema y la parte persistente de las aplicaciones. El HDD, montado en `/data`, se utiliza principalmente para multimedia, descargas y datos de gran tamaño.

| Unidad | Estado | Uso principal |
|---|---|---|
| SSD | ✅ Implementado | Ubuntu Server, LVM, stacks y datos persistentes de Docker. El espacio está completamente asignado. |
| HDD | ✅ Implementado | Multimedia, descargas y otros datos grandes. |

## SSD: sistema y aplicaciones

El SSD contiene Ubuntu Server y está completamente asignado mediante LVM. También aloja las definiciones de Docker Compose en `/opt/stacks` y los datos persistentes de los servicios en `/opt/appdata`.

Las definiciones de los stacks pueden mantenerse con el usuario habitual. La persistencia conserva permisos de grupo controlados para poder crear nuevos directorios sin elevar privilegios innecesariamente y sin alterar de forma recursiva los permisos que necesita cada aplicación.

## HDD: multimedia y datos grandes

El HDD se monta en `/data` y guarda principalmente las bibliotecas multimedia, las descargas y otros datos de gran tamaño. Las películas, las series y las descargas quedan fuera del backup remoto, mientras que `/data/documents` sí está incluido.

## Criterio de separación

La idea es sencilla: el sistema, las definiciones de los stacks y la persistencia de las aplicaciones quedan en el SSD; el contenido multimedia, las descargas y los datos que ocupan más espacio van al HDD.

Esta separación evita mezclar las bibliotecas multimedia con el disco del sistema y mantiene más clara la relación entre Docker y sus datos. No significa que todos los datos persistentes estén en el HDD: `/opt/appdata` y `/opt/stacks` están en el SSD.

## Copias de seguridad

La estrategia de copias de seguridad utiliza Kopia con almacenamiento remoto cifrado y versionado. Su funcionamiento y alcance están en [Copias de seguridad](copias-seguridad.md).
