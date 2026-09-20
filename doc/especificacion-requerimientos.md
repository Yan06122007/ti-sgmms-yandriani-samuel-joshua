# Análisis y especificación del problema y los requerimientos funcionales

**Proyecto:** Sistema de Gestión y Monitoreo de Movilidad y Seguridad — SGMMS
**Tarea Integradora — Entrega 1 (Ingeniería de Software II)**
**Equipo:** Yandriani Castañeda, Samuel Sepúlveda, Joshua García

| Campo | Descripción |
|---|---|
| **Cliente** | Alcaldía de Palmira (Secretaría de Movilidad y Seguridad), representada por el nuevo Centro de Monitoreo Urbano. |
| **Usuario** | Operador del Centro de Monitoreo, encargado de observar el estado de la ciudad, registrar y priorizar incidentes, y asignar vehículos de atención disponibles. |
| **Contexto del problema** | Palmira ha crecido en población y circulación vehicular, generando congestión en horas pico y dificultando la atención oportuna de incidentes (accidentes, robos e incendios). No existe una herramienta centralizada que permita monitorear incidentes, vehículos disponibles y la capacidad de respuesta de las unidades de atención. El SGMMS es una aplicación de escritorio en Java con interfaz gráfica que simula el comportamiento de la ciudad y apoya al operador en la identificación, priorización y atención de incidentes mediante la asignación de vehículos compatibles y disponibles. Esta primera entrega cubre únicamente la gestión de incidentes, su priorización, la gestión de vehículos de atención y la asignación de vehículos a incidentes; no incluye aún la representación gráfica del mapa, las rutas, la persistencia en archivos ni la concurrencia, que se trabajarán en entregas posteriores. |
| **Requerimientos funcionales (listado)** | RF1 – Gestionar incidentes<br>RF2 – Gestionar la prioridad de los incidentes<br>RF3 – Gestionar vehículos de atención<br>RF4 – Asignar vehículos a incidentes |
| **Requerimientos no funcionales (listado)** | RNF1 – El sistema debe informar al usuario, mediante mensajes claros, cuando una operación no pueda completarse por una condición inválida o un error.<br>RNF2 – El sistema debe mantener la consistencia de la información de incidentes y vehículos ante cualquier registro, consulta, actualización o asignación. |
| **Requerimientos de proceso (listado)** | RP1 – El código debe organizarse siguiendo el patrón de arquitectura MVC, ubicando las clases de este entregable en el paquete `model`.<br>RP2 – El diseño debe documentarse mediante diagrama de clases UML elaborado en Visual Paradigm antes de iniciar la implementación. |

---

## RF1 – Gestionar incidentes

| Campo | Descripción |
|---|---|
| **Identificador y nombre** | RF1 – Gestionar incidentes |
| **Resumen** | El sistema debe permitir al operador **registrar** un nuevo incidente de tipo accidente, robo o incendio, indicando su ubicación, gravedad, fecha y hora de generación y una descripción; **consultar** los incidentes existentes por su identificador; y **actualizar** el estado y demás datos de un incidente ya registrado (por ejemplo, al asignarle un vehículo o al resolverlo). Todo incidente registrado queda identificado con un identificador único, generado automáticamente por el sistema, y su estado inicial es *pendiente*. El sistema debe rechazar el registro de un incidente si falta información obligatoria (tipo, ubicación o gravedad) o si la ubicación no corresponde a una zona válida del mapa, e informar el error mediante un mensaje claro sin cerrar la aplicación. |
| **Entradas** | |

| Nombre entrada | Tipo de dato | Condición valores válidos |
|---|---|---|
| tipoIncidente | Enumeración (ACCIDENTE, ROBO, INCENDIO) | Debe corresponder a uno de los tres valores definidos; obligatorio. |
| ubicacion | Cadena de texto / referencia a zona del mapa | Debe corresponder a una zona o posición existente en el mapa configurado; obligatorio. |
| gravedad | Enumeración (ALTA, MEDIA, BAJA) | Debe corresponder a uno de los tres valores definidos; obligatorio. |
| fechaHora | Fecha y hora | Formato dd-mm-yyyy hh:mm; no puede ser una fecha futura; se asigna automáticamente al momento del registro si no se especifica. |
| descripcion | Cadena de texto | Máximo 200 caracteres; puede quedar vacía. |
| idIncidente (para consultar/actualizar) | Cadena de texto | Debe corresponder a un incidente previamente registrado. |

| **Resultado o Postcondición** | El incidente queda registrado (o actualizado) en el sistema con un identificador único, disponible para ser consultado, priorizado y, cuando corresponda, atendido mediante la asignación de un vehículo. |
| **Salidas** | |

| Nombre salida | Tipo de dato | Formato |
|---|---|---|
| idIncidente | Cadena de texto | Generado automáticamente por el sistema (ej. INC-001). |
| incidente (al consultar) | Objeto Incidente | Contiene id, tipo, ubicación, gravedad, fecha y hora, descripción, estado y vehículo asignado (si existe). |
| mensajeError | Cadena de texto | Se retorna únicamente cuando el registro o la actualización no puede completarse; describe la causa (ej. "Ubicación inválida"). |

---

## RF2 – Gestionar la prioridad de los incidentes

| Campo | Descripción |
|---|---|
| **Identificador y nombre** | RF2 – Gestionar la prioridad de los incidentes |
| **Resumen** | El sistema debe organizar automáticamente los incidentes activos (no resueltos) según su gravedad, dando prioridad a los de gravedad *alta* sobre *media*, y a estos sobre *baja*. Cuando dos incidentes tengan la misma gravedad, el sistema debe desempatar aplicando como criterio la fecha y hora de generación, atendiendo primero al incidente más antiguo. El sistema debe permitir al operador **consultar** en cualquier momento cuál es el incidente activo de mayor prioridad y **marcar como atendido** dicho incidente cuando el operador decide resolverlo. Si no existen incidentes activos, el sistema debe informarlo mediante un mensaje claro en lugar de retornar un valor inválido o vacío sin explicación. |
| **Entradas** | |

| Nombre entrada | Tipo de dato | Condición valores válidos |
|---|---|---|
| (ninguna; opera sobre los incidentes activos ya registrados) | — | Debe existir al menos un incidente en estado pendiente o en proceso para retornar un resultado. |

| **Resultado o Postcondición** | El operador conoce, en todo momento, cuál es el incidente de mayor prioridad entre los activos, respetando el orden gravedad alta > media > baja y el criterio de antigüedad en caso de empate. |
| **Salidas** | |

| Nombre salida | Tipo de dato | Formato |
|---|---|---|
| incidentePrioritario | Objeto Incidente | Incidente con mayor gravedad (y más antiguo en caso de empate) entre los activos. |
| mensajeError | Cadena de texto | Se retorna cuando no hay incidentes activos para priorizar (ej. "No hay incidentes activos"). |

---

## RF3 – Gestionar vehículos de atención

| Campo | Descripción |
|---|---|
| **Identificador y nombre** | RF3 – Gestionar vehículos de atención |
| **Resumen** | El sistema debe permitir **registrar** los vehículos de atención disponibles (patrulla, ambulancia o camión de bomberos), indicando su tipo y ubicación inicial; **consultar** un vehículo por su identificador para conocer su tipo, ubicación y estado actual; y **controlar los cambios de estado** de un vehículo durante el ciclo de atención de un incidente (de *disponible* a *en ruta* o *atendiendo*, y de vuelta a *disponible* cuando finaliza la atención, o a *fuera de servicio* si el equipo lo requiere). El sistema debe impedir que un vehículo cambie a un estado no contemplado o que quede en un estado inconsistente con el incidente que tiene asignado (por ejemplo, marcarlo disponible mientras sigue asignado a un incidente en proceso). |
| **Entradas** | |

| Nombre entrada | Tipo de dato | Condición valores válidos |
|---|---|---|
| tipoVehiculo | Enumeración (PATRULLA, AMBULANCIA, CAMION_BOMBEROS) | Debe corresponder a uno de los tres valores definidos; obligatorio. |
| ubicacion | Cadena de texto / referencia a zona del mapa | Debe corresponder a una posición válida del mapa configurado; obligatorio. |
| idVehiculo (para consultar/actualizar estado) | Cadena de texto | Debe corresponder a un vehículo previamente registrado. |
| nuevoEstado | Enumeración (DISPONIBLE, EN_RUTA, ATENDIENDO, FUERA_DE_SERVICIO) | Debe ser un valor válido y consistente con la situación actual del vehículo (ej. no puede pasar a DISPONIBLE si tiene un incidente en proceso asignado). |

| **Resultado o Postcondición** | El vehículo queda registrado (o su estado actualizado) y disponible para ser consultado y, cuando su estado sea *disponible*, propuesto o asignado a un incidente compatible. |
| **Salidas** | |

| Nombre salida | Tipo de dato | Formato |
|---|---|---|
| idVehiculo | Cadena de texto | Generado automáticamente por el sistema (ej. VEH-001). |
| vehiculo (al consultar) | Objeto Vehículo | Contiene id, tipo, ubicación, estado e incidente asignado (si existe). |
| mensajeError | Cadena de texto | Se retorna cuando el registro o el cambio de estado no puede completarse (ej. "Transición de estado inválida"). |

---

## RF4 – Asignar vehículos a incidentes

| Campo | Descripción |
|---|---|
| **Identificador y nombre** | RF4 – Asignar vehículos a incidentes |
| **Resumen** | El sistema debe permitir asignar un vehículo disponible a un incidente activo, validando que el tipo de vehículo sea compatible con el tipo de incidente (patrulla → robo o apoyo a accidente; ambulancia → accidente; camión de bomberos → incendio) y que el vehículo se encuentre en estado *disponible*. Antes de confirmar la asignación, el sistema debe proponer al operador un vehículo candidato adecuado (disponible y compatible) para el incidente indicado. Si la asignación solicitada no es válida — vehículo no disponible, tipo incompatible con el incidente, o incidente ya resuelto o con vehículo asignado — el sistema debe rechazar la operación e informar la causa mediante una excepción controlada y un mensaje claro al usuario, sin afectar el resto de la simulación. Al confirmarse una asignación válida, el incidente pasa a estado *en proceso*, se asocia al vehículo, y el vehículo cambia su estado a *en ruta* o *atendiendo*. |
| **Entradas** | |

| Nombre entrada | Tipo de dato | Condición valores válidos |
|---|---|---|
| idIncidente | Cadena de texto | Debe corresponder a un incidente registrado que no esté en estado *resuelto* ni tenga ya un vehículo asignado. |
| idVehiculo | Cadena de texto | Debe corresponder a un vehículo registrado, en estado *disponible* y de tipo compatible con el incidente. |

| **Resultado o Postcondición** | El incidente queda asociado al vehículo asignado y cambia a estado *en proceso*; el vehículo queda asociado al incidente y cambia su estado a *en ruta* o *atendiendo*. Si la asignación no cumple las condiciones de validez, no se modifica el estado de ninguno de los dos objetos y se informa el error. |
| **Salidas** | |

| Nombre salida | Tipo de dato | Formato |
|---|---|---|
| vehiculoCandidato | Objeto Vehículo | Sugerido por el sistema antes de confirmar la asignación (disponible y compatible con el incidente). |
| confirmacionAsignacion | Booleano / Objeto Incidente actualizado | Indica si la asignación fue realizada y refleja el nuevo estado del incidente y del vehículo. |
| mensajeError | Cadena de texto | Se retorna cuando la asignación es rechazada (ej. "Vehículo no disponible", "Tipo de vehículo incompatible con el incidente"). |
