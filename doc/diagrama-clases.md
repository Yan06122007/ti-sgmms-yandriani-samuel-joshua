# Diagrama de clases — Paquete `model` (Entrega 1)

**Proyecto:** SGMMS — Tarea Integradora, Entrega 1 (Ingeniería de Software II)
**Equipo:** Yandriani Castañeda, Samuel Sepúlveda, Joshua García

> ⚠️ Este documento contiene el **diseño completo y detallado** del diagrama de clases
> (clases, atributos, métodos, tipos, relaciones y multiplicidades) que el equipo debe
> **reconstruir en Visual Paradigm**, siguiendo exactamente lo especificado aquí. La
> imagen `diagrama-clases.png` (incluida en esta carpeta) sirve como guía visual de
> referencia mientras se dibuja el diagrama definitivo en la herramienta; el archivo
> `.vpp` y su exportación a PDF/imagen deben generarse desde Visual Paradigm y
> subirse junto con este documento, tal como pide el estándar del repositorio.

![Diagrama de clases de referencia](diagrama-clases.png)

## Alcance

El diagrama corresponde **únicamente al paquete `model`**, incluyendo la clase
`Controller` (aquí llamada `SGMMSController`), y cubre exclusivamente los cuatro
requerimientos funcionales de esta entrega (RF1–RF4). No incluye mapa, rutas,
persistencia en archivos ni concurrencia: esos elementos se incorporarán en
entregas posteriores.

## Enumeraciones

| Enumeración | Valores | Usada por |
|---|---|---|
| `TipoIncidente` | `ACCIDENTE`, `ROBO`, `INCENDIO` | `Incidente` |
| `Gravedad` | `ALTA`, `MEDIA`, `BAJA` | `Incidente` (orden de prioridad: ALTA > MEDIA > BAJA) |
| `EstadoIncidente` | `PENDIENTE`, `EN_PROCESO`, `RESUELTO` | `Incidente` |
| `TipoVehiculo` | `PATRULLA`, `AMBULANCIA`, `CAMION_BOMBEROS` | `Vehiculo` |
| `EstadoVehiculo` | `DISPONIBLE`, `EN_RUTA`, `ATENDIENDO`, `FUERA_DE_SERVICIO` | `Vehiculo` |

## Clases del modelo

### `Incidente`
Representa un incidente registrado en el sistema (RF1) y provee el criterio de
comparación usado para priorizarlo (RF2).

**Atributos**
- `- id : String`
- `- tipo : TipoIncidente`
- `- ubicacion : String`
- `- gravedad : Gravedad`
- `- fechaHora : LocalDateTime`
- `- descripcion : String`
- `- estado : EstadoIncidente`
- `- vehiculoAsignado : Vehiculo`

**Métodos**
- `+ Incidente(tipo, ubicacion, gravedad, descripcion)` — constructor; genera el `id`, fija `fechaHora` al momento actual y `estado = PENDIENTE`.
- `+ getId() : String`
- `+ getTipo() : TipoIncidente`
- `+ getGravedad() : Gravedad`
- `+ getEstado() : EstadoIncidente`
- `+ getVehiculoAsignado() : Vehiculo`
- `+ setEstado(estado: EstadoIncidente) : void`
- `+ asignarVehiculo(vehiculo: Vehiculo) : void` — asocia el vehículo y cambia el estado a `EN_PROCESO` (RF4).
- `+ marcarResuelto() : void` — cambia el estado a `RESUELTO` (RF2).
- `+ compareTo(otro: Incidente) : int` — implementa `Comparable<Incidente>`; ordena por `gravedad` (ALTA > MEDIA > BAJA) y desempata por `fechaHora` (más antiguo primero) (RF2).

### `Vehiculo`
Representa un vehículo de atención (RF3) y valida su compatibilidad con un
incidente (RF4).

**Atributos**
- `- id : String`
- `- tipo : TipoVehiculo`
- `- ubicacion : String`
- `- estado : EstadoVehiculo`
- `- incidenteAsignado : Incidente`

**Métodos**
- `+ Vehiculo(tipo, ubicacion)` — constructor; genera el `id` y fija `estado = DISPONIBLE`.
- `+ getId() : String`
- `+ getTipo() : TipoVehiculo`
- `+ getEstado() : EstadoVehiculo`
- `+ getIncidenteAsignado() : Incidente`
- `+ setEstado(estado: EstadoVehiculo) : void` — valida transiciones consistentes (RF3).
- `+ estaDisponible() : boolean`
- `+ esCompatibleCon(incidente: Incidente) : boolean` — aplica las reglas: patrulla→robo/apoyo accidente, ambulancia→accidente, camión de bomberos→incendio (RF4).
- `+ asignarIncidente(incidente: Incidente) : void`
- `+ liberar() : void` — vuelve el vehículo a `DISPONIBLE` y limpia `incidenteAsignado`.

### `GestorIncidentes`
Encapsula la colección de incidentes y las operaciones de RF1 y RF2.

**Atributos**
- `- incidentes : List<Incidente>`

**Métodos**
- `+ registrarIncidente(incidente: Incidente) : void`
- `+ consultarIncidente(id: String) : Incidente` — lanza `IncidenteNoEncontradoException` si no existe.
- `+ actualizarIncidente(id: String, ...) : void`
- `+ obtenerIncidentePrioritario() : Incidente` — aplica el orden definido en `Incidente.compareTo`.
- `+ listarIncidentesActivos() : List<Incidente>`

### `GestorVehiculos`
Encapsula la colección de vehículos y las operaciones de RF3, y apoya a RF4
proponiendo candidatos.

**Atributos**
- `- vehiculos : List<Vehiculo>`

**Métodos**
- `+ registrarVehiculo(vehiculo: Vehiculo) : void`
- `+ consultarVehiculo(id: String) : Vehiculo` — lanza `VehiculoNoEncontradoException` si no existe.
- `+ actualizarEstado(id: String, estado: EstadoVehiculo) : void`
- `+ buscarCandidatoDisponible(incidente: Incidente) : Vehiculo` — retorna el primer vehículo disponible y compatible con el incidente.

### `SGMMSController`
Clase de control (patrón MVC) que orquesta los casos de uso de esta entrega,
delegando en `GestorIncidentes` y `GestorVehiculos`.

**Atributos**
- `- gestorIncidentes : GestorIncidentes`
- `- gestorVehiculos : GestorVehiculos`

**Métodos**
- `+ registrarIncidente(...) : void`
- `+ consultarIncidente(id: String) : Incidente`
- `+ consultarIncidentePrioritario() : Incidente`
- `+ registrarVehiculo(...) : void`
- `+ consultarVehiculo(id: String) : Vehiculo`
- `+ proponerVehiculoCandidato(idIncidente: String) : Vehiculo`
- `+ asignarVehiculoAIncidente(idIncidente: String, idVehiculo: String) : void` — valida disponibilidad y compatibilidad; si la asignación no es válida lanza `AsignacionInvalidaException`.

### Excepciones del dominio
- `IncidenteNoEncontradoException` — se lanza cuando se consulta/actualiza un `id` de incidente inexistente.
- `VehiculoNoEncontradoException` — se lanza cuando se consulta/actualiza un `id` de vehículo inexistente.
- `AsignacionInvalidaException` — se lanza cuando se intenta asignar un vehículo no disponible, incompatible, o a un incidente ya resuelto/con vehículo asignado.

## Relaciones y multiplicidades

| Origen | Relación | Destino | Multiplicidad |
|---|---|---|---|
| `SGMMSController` | asociación (usa) | `GestorIncidentes` | 1 → 1 |
| `SGMMSController` | asociación (usa) | `GestorVehiculos` | 1 → 1 |
| `GestorIncidentes` | agregación | `Incidente` | 1 → 0..* |
| `GestorVehiculos` | agregación | `Vehiculo` | 1 → 0..* |
| `Incidente` | asociación (`vehiculoAsignado` / `incidenteAsignado`) | `Vehiculo` | 0..1 → 0..1 |
| `Incidente` | dependencia | `TipoIncidente`, `Gravedad`, `EstadoIncidente` | — |
| `Vehiculo` | dependencia | `TipoVehiculo`, `EstadoVehiculo` | — |
| `GestorIncidentes` | dependencia (lanza) | `IncidenteNoEncontradoException` | — |
| `GestorVehiculos` | dependencia (lanza) | `VehiculoNoEncontradoException` | — |
| `SGMMSController` | dependencia (lanza) | `AsignacionInvalidaException` | — |

## Trazabilidad requerimiento → clases/métodos

| Requerimiento | Clases / métodos responsables |
|---|---|
| RF1 – Gestionar incidentes | `Incidente` (constructor, getters, `setEstado`), `GestorIncidentes` (`registrarIncidente`, `consultarIncidente`, `actualizarIncidente`), `SGMMSController` (métodos equivalentes) |
| RF2 – Gestionar la prioridad de los incidentes | `Incidente.compareTo`, `GestorIncidentes.obtenerIncidentePrioritario`, `Incidente.marcarResuelto`, `SGMMSController.consultarIncidentePrioritario` |
| RF3 – Gestionar vehículos de atención | `Vehiculo` (constructor, getters, `setEstado`, `estaDisponible`), `GestorVehiculos` (`registrarVehiculo`, `consultarVehiculo`, `actualizarEstado`) |
| RF4 – Asignar vehículos a incidentes | `Vehiculo.esCompatibleCon`, `GestorVehiculos.buscarCandidatoDisponible`, `Incidente.asignarVehiculo`, `Vehiculo.asignarIncidente`, `SGMMSController.proponerVehiculoCandidato`, `SGMMSController.asignarVehiculoAIncidente`, `AsignacionInvalidaException` |
