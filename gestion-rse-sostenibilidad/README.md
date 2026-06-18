# Gestión de Responsabilidad Social y Sostenibilidad

Proceso de negocio modelado en BPMN para su implementación ejecutable en **BonitaSoft**.

## Contenido

| Archivo | Descripción |
| --- | --- |
| `Diagrama_P5_RSE_ejecutable.bpmn` | Diagrama BPMN 2.0 ejecutable (tareas humanas/sistema tipificadas + condiciones en gateways). Es el archivo que se importa en Bonita Studio. |
| `Implementacion_P5_RSE.md` | Especificación para completar dentro de Studio lo que el `.bpmn` no transporta: Business Data Model, variables, contratos/restricciones, formularios y actores. |

## Requisitos para ejecutar en otra PC

- **Java JDK 17** — https://www.oracle.com/java/technologies/downloads/#java17
- **Bonita Studio Community 2023.2** — https://www.bonitasoft.com/es/descargas

## Pasos para reproducir el proceso

1. Abrir Bonita Studio y crear (o abrir) un proyecto.
2. **File → Import → BPMN 2.0** y seleccionar `Diagrama_P5_RSE_ejecutable.bpmn`.
   Quedan creados: pools/lanes, eventos, las 3 compuertas con sus condiciones y las
   tareas ya tipificadas (humanas vs. de sistema).
3. Seguir `Implementacion_P5_RSE.md` para configurar dentro de Studio:
   - **8.b** Business Data Model (5 clases) → genera el diagrama de clases UML.
   - **8.c** Variable de negocio `iniciativa : IniciativaRSE`.
   - **8.d** Contratos y restricciones (instanciación + tareas humanas).
   - **8.e** Formularios (UI Forms) de cada tarea humana.
   - **8.f** Actores (3) mapeados a lanes y a grupos/roles de la Organization.
4. Crear la **Organization** con los usuarios indicados y hacer el *Actor Mapping*.
5. **Run** para desplegar y validar en el Bonita Portal.

> Nota: ningún `.bpmn` embebe el BDM, contratos ni formularios de Bonita; esos elementos
> son propios de Studio y por eso se documentan en `Implementacion_P5_RSE.md`. Para una
> copia 100 % lista para ejecutar sin reconfigurar, exportar desde Studio el archivo `.bos`
> (*File → Export*) una vez completados los pasos anteriores.

## Roles involucrados

- Coordinador de RSE (iniciador)
- Comité de Sostenibilidad / Gerencia
- Áreas Operativas (Tiendas / CD / Logística)
- Sistema (Plataforma de Sostenibilidad / ERP) — tareas automáticas
