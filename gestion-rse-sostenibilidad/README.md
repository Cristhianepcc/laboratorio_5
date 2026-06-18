# Gestión de Responsabilidad Social y Sostenibilidad

Proceso de negocio modelado en BPMN para su implementación ejecutable en **BonitaSoft**.

## Contenido

| Archivo | Descripción |
| --- | --- |
| `Proceso_RSE_Sodimac.bos` | **Proyecto Bonita completo y ejecutable** (diagrama + BDM + variables + contratos + formularios + actores + organización). Es lo que hay que importar para replicar el proceso sin reconfigurar nada. |
| `Diagrama_P5_RSE_ejecutable.bpmn` | Diagrama BPMN 2.0 ejecutable (tareas humanas/sistema tipificadas + condiciones en gateways). Útil solo para ver/importar el diagrama; **no** lleva BDM/contratos/forms/actores. |
| `Implementacion_P5_RSE.md` | Especificación de referencia del BDM, variables, contratos/restricciones, formularios y actores (sirve para reconstruir o documentar). |

## Requisitos para ejecutar en otra PC

- **Java JDK 17** — https://www.oracle.com/java/technologies/downloads/#java17
- **Bonita Studio Community 2023.2** — https://www.bonitasoft.com/es/descargas

## Replicar el proceso en otra PC (recomendado: usar el `.bos`)

1. Abrir **Bonita Studio**.
2. **File → Import** → seleccionar **`Proceso_RSE_Sodimac.bos`** → Finish.
   Se importa todo: diagrama, Business Data Model, variables, contratos, formularios,
   actores y la organización `RSE_Sodimac`.
3. **Deploy del Business Data Model**: abrir el editor del BDM (Overview → Business Data
   Model) → **Deploy**.
4. **Activar la organización**: en *Project explorer* → `RSE_Sodimac.xml` → **Set as active**
   (o **Deploy** desde el editor de organización). Usuario por defecto: `coordinador.rse`.
5. Verificar el **Actor mapping** en **Configure** (los 3 actores → grupos
   `sostenibilidad` / `gerencia` / `operaciones`).
6. **Run** para desplegar y validar en el Bonita Portal.

### Usuarios de prueba (organización `RSE_Sodimac`, password `bpm12345`)

| Usuario | Grupo | Actor / rol en el proceso |
| --- | --- | --- |
| `coordinador.rse` | sostenibilidad | Coordinador de RSE (iniciador) |
| `comite.sost` | gerencia | Comité de Sostenibilidad / Gerencia |
| `operario` | operaciones | Áreas Operativas |

> Nota: el `.bpmn` por sí solo no embebe el BDM, contratos ni formularios; para una copia
> 100 % lista para ejecutar usa el **`.bos`**.

### Troubleshooting

- **Error 400 "Request header is too large" al completar una tarea**: el navegador tiene
  demasiadas cookies para `localhost` (de otros proyectos). Abre la app en una **ventana de
  incógnito** o borra las cookies de `localhost:8080`.
- **"Pool element name contains a forbidden character"**: el nombre del pool/proceso debe
  ser ASCII (sin tildes ni símbolos).
- **"No process initiator defined for Comunidad y Proveedores"**: es el pool externo (caja
  negra); selecciona el pool principal antes de pulsar Run, o ignora el aviso y arranca el
  proceso desde la pestaña *Processes*.

## Roles involucrados

- Coordinador de RSE (iniciador)
- Comité de Sostenibilidad / Gerencia
- Áreas Operativas (Tiendas / CD / Logística)
- Sistema (Plataforma de Sostenibilidad / ERP) — tareas automáticas
