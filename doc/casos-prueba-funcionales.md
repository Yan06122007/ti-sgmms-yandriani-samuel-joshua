# Diseño de casos de prueba funcionales

**Proyecto:** SGMMS — Tarea Integradora, Entrega 1 (Ingeniería de Software II)
**Equipo:** Yandriani Castañeda, Samuel Sepúlveda, Joshua García
**Sprint/Iteración:** Entrega 1

---

## RF1 – Gestionar incidentes

| ID Caso | Nombre Caso (Objetivo) | Precondición | Datos de Entrada | Pasos | Resultado Esperado |
|---|---|---|---|---|---|
| CP1-01 | Registrar un incidente válido | El sistema está en ejecución y el mapa tiene configuradas las zonas. | tipo=INCENDIO, ubicacion="Zona Comercial 3", gravedad=ALTA, descripcion="Incendio en local comercial" | 1. Seleccionar "Registrar incidente".<br>2. Ingresar tipo=INCENDIO.<br>3. Ingresar ubicación="Zona Comercial 3".<br>4. Ingresar gravedad=ALTA.<br>5. Ingresar descripción.<br>6. Confirmar registro. | El sistema crea el incidente con estado PENDIENTE, le asigna un id único (ej. INC-004) y fecha/hora actual, y lo muestra en la lista de incidentes activos. |
| CP1-02 | Rechazar registro con ubicación inexistente en el mapa | El sistema está en ejecución. | tipo=ROBO, ubicacion="Zona-99" (no existe en el mapa), gravedad=MEDIA | 1. Seleccionar "Registrar incidente".<br>2. Ingresar tipo=ROBO.<br>3. Ingresar ubicación="Zona-99".<br>4. Ingresar gravedad=MEDIA.<br>5. Confirmar registro. | El sistema rechaza el registro, muestra el mensaje "Ubicación inválida" y no crea ningún incidente. |
| CP1-03 | Consultar un incidente existente por id | Existe un incidente registrado con id="INC-001". | idIncidente="INC-001" | 1. Seleccionar "Consultar incidente".<br>2. Ingresar id="INC-001".<br>3. Confirmar búsqueda. | El sistema muestra los datos completos del incidente INC-001 (tipo, ubicación, gravedad, fecha/hora, descripción, estado, vehículo asignado si existe). |
| CP1-04 | Consultar un incidente con id inexistente | No existe ningún incidente con id="INC-999". | idIncidente="INC-999" | 1. Seleccionar "Consultar incidente".<br>2. Ingresar id="INC-999".<br>3. Confirmar búsqueda. | El sistema informa el error "Incidente no encontrado" (excepción `IncidenteNoEncontradoException` controlada) y no interrumpe la aplicación. |

## RF2 – Gestionar la prioridad de los incidentes

| ID Caso | Nombre Caso (Objetivo) | Precondición | Datos de Entrada | Pasos | Resultado Esperado |
|---|---|---|---|---|---|
| CP2-01 | Consultar el incidente de mayor prioridad con gravedades distintas | Existen tres incidentes activos: INC-001 (gravedad BAJA), INC-002 (gravedad ALTA), INC-003 (gravedad MEDIA). | Ninguno (consulta directa). | 1. Seleccionar "Consultar incidente prioritario" en el Panel de Incidentes. | El sistema retorna INC-002 (gravedad ALTA) como incidente de mayor prioridad. |
| CP2-02 | Desempatar por antigüedad entre incidentes de igual gravedad | Existen dos incidentes activos con gravedad ALTA: INC-005 (generado a las 08:00) e INC-006 (generado a las 08:15). | Ninguno (consulta directa). | 1. Seleccionar "Consultar incidente prioritario" en el Panel de Incidentes. | El sistema retorna INC-005 por ser el más antiguo entre los dos de gravedad ALTA. |
| CP2-03 | Consultar el incidente prioritario cuando no hay incidentes activos | No existe ningún incidente en estado PENDIENTE o EN_PROCESO (todos están RESUELTO o no hay ninguno registrado). | Ninguno (consulta directa). | 1. Seleccionar "Consultar incidente prioritario" en el Panel de Incidentes. | El sistema informa el mensaje "No hay incidentes activos" en lugar de mostrar un incidente o fallar. |

## RF3 – Gestionar vehículos de atención

| ID Caso | Nombre Caso (Objetivo) | Precondición | Datos de Entrada | Pasos | Resultado Esperado |
|---|---|---|---|---|---|
| CP3-01 | Registrar un vehículo válido | El sistema está en ejecución. | tipo=AMBULANCIA, ubicacion="Zona Residencial 1" | 1. Seleccionar "Registrar vehículo".<br>2. Ingresar tipo=AMBULANCIA.<br>3. Ingresar ubicación="Zona Residencial 1".<br>4. Confirmar registro. | El sistema crea el vehículo con estado DISPONIBLE, le asigna un id único (ej. VEH-003) y lo muestra en la lista de vehículos. |
| CP3-02 | Rechazar cambio de estado inconsistente | Existe el vehículo VEH-001, en estado ATENDIENDO, con el incidente INC-002 asignado y aún no resuelto. | idVehiculo="VEH-001", nuevoEstado=DISPONIBLE | 1. Seleccionar "Actualizar estado de vehículo".<br>2. Ingresar id="VEH-001".<br>3. Seleccionar nuevoEstado=DISPONIBLE.<br>4. Confirmar. | El sistema rechaza el cambio de estado y muestra el mensaje "Transición de estado inválida: el vehículo tiene un incidente en proceso"; el vehículo permanece en estado ATENDIENDO. |
| CP3-03 | Consultar un vehículo existente por id | Existe el vehículo VEH-002 registrado, en estado DISPONIBLE. | idVehiculo="VEH-002" | 1. Seleccionar "Consultar vehículo".<br>2. Ingresar id="VEH-002".<br>3. Confirmar búsqueda. | El sistema muestra tipo, ubicación, estado e incidente asignado (ninguno) del vehículo VEH-002. |
| CP3-04 | Consultar un vehículo con id inexistente | No existe ningún vehículo con id="VEH-999". | idVehiculo="VEH-999" | 1. Seleccionar "Consultar vehículo".<br>2. Ingresar id="VEH-999".<br>3. Confirmar búsqueda. | El sistema informa el error "Vehículo no encontrado" (excepción `VehiculoNoEncontradoException` controlada) sin cerrar la aplicación. |

## RF4 – Asignar vehículos a incidentes

| ID Caso | Nombre Caso (Objetivo) | Precondición | Datos de Entrada | Pasos | Resultado Esperado |
|---|---|---|---|---|---|
| CP4-01 | Asignar correctamente un vehículo compatible y disponible | Existe el incidente INC-007 (tipo INCENDIO, estado PENDIENTE, sin vehículo asignado) y el vehículo VEH-004 (tipo CAMION_BOMBEROS, estado DISPONIBLE). | idIncidente="INC-007", idVehiculo="VEH-004" | 1. Seleccionar el incidente INC-007 en el Panel de Incidentes.<br>2. Seleccionar "Proponer vehículo candidato" (el sistema sugiere VEH-004).<br>3. Confirmar la asignación. | El incidente INC-007 pasa a estado EN_PROCESO y queda asociado a VEH-004; el vehículo VEH-004 pasa a estado ATENDIENDO y queda asociado a INC-007. |
| CP4-02 | Rechazar asignación por tipo de vehículo incompatible | Existe el incidente INC-008 (tipo INCENDIO, estado PENDIENTE) y el vehículo VEH-005 (tipo AMBULANCIA, estado DISPONIBLE). | idIncidente="INC-008", idVehiculo="VEH-005" | 1. Seleccionar el incidente INC-008 en el Panel de Incidentes.<br>2. Intentar asignar manualmente el vehículo VEH-005.<br>3. Confirmar la asignación. | El sistema rechaza la asignación, lanza `AsignacionInvalidaException` y muestra el mensaje "Tipo de vehículo incompatible con el incidente"; ni el incidente ni el vehículo cambian de estado. |
| CP4-03 | Rechazar asignación de un vehículo no disponible | Existe el incidente INC-009 (tipo ROBO, estado PENDIENTE) y el vehículo VEH-006 (tipo PATRULLA, estado EN_RUTA, ya asignado a otro incidente). | idIncidente="INC-009", idVehiculo="VEH-006" | 1. Seleccionar el incidente INC-009 en el Panel de Incidentes.<br>2. Intentar asignar manualmente el vehículo VEH-006.<br>3. Confirmar la asignación. | El sistema rechaza la asignación, lanza `AsignacionInvalidaException` y muestra el mensaje "Vehículo no disponible"; el incidente permanece PENDIENTE y sin vehículo asignado. |
| CP4-04 | Rechazar asignación sobre un incidente ya resuelto | Existe el incidente INC-010 en estado RESUELTO y el vehículo VEH-007 (tipo PATRULLA, estado DISPONIBLE). | idIncidente="INC-010", idVehiculo="VEH-007" | 1. Seleccionar el incidente INC-010 (resuelto) en el historial de incidentes.<br>2. Intentar asignar el vehículo VEH-007.<br>3. Confirmar la asignación. | El sistema rechaza la asignación, lanza `AsignacionInvalidaException` y muestra el mensaje "El incidente ya fue resuelto"; el vehículo VEH-007 permanece DISPONIBLE. |

---

### Instrucciones para el Estudiante (verificadas en este diseño)
- El nombre del caso describe claramente el comportamiento verificado.
- Cada caso está asociado a un requerimiento existente (RF1–RF4).
- El resultado esperado es verificable y específico (estados, mensajes y excepciones concretas).
- Los pasos están numerados y son reproducibles.
- Cada requerimiento incluye al menos un caso válido y un caso inválido, aplicando técnicas de caja negra (partición de equivalencia sobre disponibilidad, compatibilidad y existencia de identificadores).
