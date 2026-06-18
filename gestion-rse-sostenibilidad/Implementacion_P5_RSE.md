# Implementación BonitaSoft – Gestión de Responsabilidad Social y Sostenibilidad

Especificación para llevar el proceso **Gestión de RSE y Sostenibilidad** desde el modelo BPMN
hasta el paso **8.f** del Lab 5 (actores mapeados a roles). Cubre las actividades **8.a → 8.f**.

Diagrama ejecutable: `Diagrama_P5_RSE_ejecutable.bpmn`

---

## 8.a — Diagrama ejecutable (ya aplicado en el `.bpmn`)

- `isExecutable = true` en el proceso principal.
- **Tareas humanas (userTask):** `t1` Diagnosticar, `t2` Formular propuesta, `teval` Evaluar,
  `t6` Planificar, `texec` Ejecutar en campo, `t9` Monitorear, `t10` Acciones correctivas,
  `taprob` Aprobar publicación.
- **Tareas de sistema (serviceTask / automáticas):** `tkpi` Consolidar KPIs, `tnotif` Notificar,
  `tevid` Registrar evidencias, `treport` Generar reporte, `tpub` Publicar y archivar.
- **Condiciones reales en gateways** (expresiones sobre la variable `iniciativa`):

| Gateway | Flujo | Condición |
| --- | --- | --- |
| gw1 ¿Requiere comité? | f5 Sí | `iniciativa.requierePresupuesto == true` |
| gw1 | f6 No | `iniciativa.requierePresupuesto == false` |
| gw2 ¿Aprobada? | f9 Sí | `iniciativa.aprobada == true` |
| gw2 | f8 No | `iniciativa.aprobada == false` |
| gw3 ¿Metas cumplidas? | f17 Sí | `iniciativa.metasCumplidas == true` |
| gw3 | f15 No | `iniciativa.metasCumplidas == false` |

> En Bonita Studio: importar el `.bpmn`, luego en cada flujo de salida de los gateways usar
> **Decision → Expression** con estas condiciones (default flow en la rama "No" si se prefiere).

---

## 8.b — Business Data Model (BDM)

Crear en *Development → Business Data Model* las siguientes clases (paquete `com.company.rse`):

### `IniciativaRSE` (objeto raíz, una por instancia de proceso)
| Atributo | Tipo | Notas |
| --- | --- | --- |
| codigo | STRING | autogenerado / único |
| nombre | STRING | obligatorio |
| tipo | STRING | RECICLAJE \| EFICIENCIA_ENERGETICA \| VOLUNTARIADO \| PROVEEDORES_SOSTENIBLES |
| descripcion | TEXT | |
| requierePresupuesto | BOOLEAN | dirige gw1 |
| presupuestoSolicitado | DOUBLE | |
| presupuestoAprobado | DOUBLE | lo fija el comité |
| estado | STRING | DIAGNOSTICO, FORMULADA, EN_EVALUACION, APROBADA, ARCHIVADA, EN_EJECUCION, EN_MONITOREO, REPORTADA, PUBLICADA |
| aprobada | BOOLEAN | dirige gw2 |
| metasCumplidas | BOOLEAN | dirige gw3 |
| fechaInicio | DATE | |
| fechaFin | DATE | |
| indicadores | Relación 1..* `IndicadorKPI` | composición |
| evidencias | Relación 0..* `EvidenciaAvance` | composición |
| reporte | Relación 0..1 `ReporteSostenibilidad` | composición |
| acciones | Relación 0..* `AccionCorrectiva` | composición |

### `IndicadorKPI`
| Atributo | Tipo |
| --- | --- |
| nombre | STRING (consumo energético, agua, residuos, mermas) |
| unidad | STRING |
| valorLineaBase | DOUBLE |
| valorActual | DOUBLE |
| meta | DOUBLE |

### `EvidenciaAvance`
| Atributo | Tipo |
| --- | --- |
| fecha | DATE |
| descripcion | STRING |
| porcentajeAvance | DOUBLE |
| archivoUrl | STRING |

### `ReporteSostenibilidad`
| Atributo | Tipo |
| --- | --- |
| codigo | STRING |
| fechaGeneracion | DATE |
| resumen | TEXT |
| urlPublicacion | STRING |
| aprobadoPublicacion | BOOLEAN |

### `AccionCorrectiva`
| Atributo | Tipo |
| --- | --- |
| descripcion | STRING |
| responsable | STRING |
| fecha | DATE |

> El **Diagrama de Clases UML** (entregable A.3.iii) se genera/exporta desde esta vista del BDM.

---

## 8.c — Variables de negocio (Business Variables)

A nivel de **proceso** (pestaña *Data → Business Variables*):

| Variable | Tipo (BDM) | Inicialización |
| --- | --- | --- |
| `iniciativa` | `IniciativaRSE` | nueva instancia desde el contrato de instanciación |

Variables de proceso (transitorias) opcionales:

| Variable | Tipo | Uso |
| --- | --- | --- |
| `comentarioComite` | String | observaciones de la evaluación |
| `observacionPublicacion` | String | observaciones del comité al publicar |

---

## 8.d — Contratos (Contracts) y Restricciones (Constraints)

### Contrato de instanciación (formulario de inicio)
Inputs: `nombreInput` (text), `tipoInput` (text), `descripcionInput` (text),
`requierePresupuestoInput` (boolean), `presupuestoInput` (decimal), `metaInput` (decimal).

**Constraints:**
- `nombreInput.empty == false` — "El nombre de la iniciativa es obligatorio."
- `requierePresupuestoInput == false || presupuestoInput > 0` — "Si requiere presupuesto, debe ser > 0."
- `presupuestoInput >= 0` — "El presupuesto no puede ser negativo."

### Contrato tarea `teval` (Evaluar iniciativa y presupuesto)
Inputs: `aprobadaInput` (boolean), `presupuestoAprobadoInput` (decimal), `comentarioInput` (text).
**Constraints:**
- `aprobadaInput == false || presupuestoAprobadoInput > 0` — "Una iniciativa aprobada requiere monto > 0."

### Contrato tarea `t9` (Monitorear y medir impacto)
Inputs: `metasCumplidasInput` (boolean), `resultadoInput` (decimal).

### Contrato tarea `taprob` (Aprobar publicación)
Inputs: `aprobarPublicacionInput` (boolean), `observacionInput` (text).

### Contrato tarea `texec` / `t10`
- `texec`: `descripcionEvidenciaInput` (text), `porcentajeInput` (decimal) — constraint `porcentajeInput >= 0 && porcentajeInput <= 100`.
- `t10`: `descripcionAccionInput` (text, obligatorio).

Las operaciones (Operations) de cada tarea mapean estos inputs a los campos de `iniciativa`
(p. ej. `teval` → `iniciativa.aprobada = aprobadaInput`, `iniciativa.presupuestoAprobado = presupuestoAprobadoInput`).

---

## 8.e — Formularios (UI Forms)

| Tarea | Formulario | Campos principales |
| --- | --- | --- |
| Instanciación | **Formular iniciativa RSE** | nombre, tipo (combo), descripción, ¿requiere presupuesto?, presupuesto, meta |
| `teval` | **Evaluar iniciativa** (Comité) | datos read-only de la iniciativa + ¿aprobar?, monto aprobado, comentario |
| `texec` | **Ejecutar en campo** (Áreas Op.) | descripción de evidencia, % avance, adjuntar archivo |
| `t9` | **Monitorear impacto** | KPIs read-only + resultado, ¿metas cumplidas? |
| `t10` | **Registrar acción correctiva** | descripción, responsable |
| `taprob` | **Aprobar publicación del reporte** | reporte read-only + ¿aprobar publicación?, observación |

> Las tareas de sistema (`tkpi`, `tnotif`, `tevid`, `treport`, `tpub`) **no llevan formulario**:
> se implementan con conectores/scripts (Groovy) o conectores email para `tnotif`.

---

## 8.f — Actores y mapeo a roles (mínimo 2)

En *Process → Actors* definir los actores y enlazarlos a las lanes; el **actor iniciador** es
el Coordinador de RSE.

| Actor (Bonita) | Lane | Rol / Grupo en la organización (Organization) | Tareas |
| --- | --- | --- | --- |
| `actorCoordinadorRSE` ⭐ iniciador | Coordinador de RSE | Grupo: `Área de Sostenibilidad` / Rol: `Coordinador RSE` | t1, t2, t6, t9, t10 |
| `actorComite` | Comité de Sostenibilidad / Gerencia | Grupo: `Gerencia` / Rol: `Miembro Comité Sostenibilidad` | teval, taprob |
| `actorAreasOperativas` | Áreas Operativas (Tiendas / CD / Logística) | Grupo: `Operaciones` / Rol: `Operario` | texec |

> El lane **Sistema (Plataforma / ERP)** no se mapea a un actor: sus tareas son automáticas.
> Esto cumple el mínimo de 2 actores (aquí hay 3) y deja el proceso listo para la
> **Actor Mapping** contra usuarios reales de la Organization en el paso 8.h (validación).

### Organization sugerida (Bonita Portal → Organization)
- Grupo `Área de Sostenibilidad` → usuario `coordinador.rse`
- Grupo `Gerencia` → usuario `comite.sostenibilidad`
- Grupo `Operaciones` → usuario `operario.tienda`

---

## Pendiente después de 8.f (fuera del alcance pedido)
- 8.g Rediseño integrando todo lo anterior · 8.h Validación ejecutando en el Portal.
- Entregables: app de seguimiento de instancias (A.3.viii), repo GitHub (C), simulación (B).
