---
title: "Product Requirements Document: home-app-new"
status: "complete"
created: "2026-04-28"
updated: "2026-04-29"
workflowType: "prd"
project: "home-app-new"
stepsCompleted:
  - "step-01-init"
  - "step-02-discovery"
  - "step-02b-vision"
  - "step-02c-executive-summary"
  - "step-03-success"
  - "step-01b-continue"
  - "step-04-journeys"
  - "step-05-domain"
  - "step-06-innovation"
  - "step-07-project-type"
  - "step-08-scoping"
  - "step-09-functional"
  - "step-10-nonfunctional"
  - "step-11-polish"
  - "step-12-complete"
releaseMode: "phased"
inputDocuments:
  - "_bmad-output/planning-artifacts/product-brief-home-app-new.md"
  - "_bmad-output/planning-artifacts/product-brief-home-app-new-distillate.md"
documentCounts:
  briefCount: 2
  researchCount: 0
  brainstormingCount: 0
  projectDocsCount: 0
classification:
  projectType: "web_app"
  domain: "general"
  complexity: "low"
  projectContext: "greenfield"
---

# Product Requirements Document - home-app-new

**Autor:** Dani | **Fecha:** 2026-04-28 | **Tipo:** Web App MPA | **Contexto:** Greenfield

## Executive Summary

Home App New es una aplicacion web responsive para hogares pequenos que necesitan coordinar informacion domestica de alta frecuencia. El problema central es la fragmentacion: inventario, faltantes y lista de compra estan dispersos entre memoria, chats y apps separadas, generando olvidos, compras duplicadas y perdida de tiempo.

El MVP prioriza inventario domestico y consumibles con lista de compra, disenados para consulta y actualizacion rapida en movil. La propuesta de valor es una unica fuente confiable y compartida que resuelve decisiones puntuales en momentos de alta urgencia y baja paciencia.

**Diferenciador central:** No mas funcionalidades, sino integracion util de tareas de alta recurrencia en una sola experiencia. Home App New compite contra la fragmentacion — reemplaza saltos entre WhatsApp, notas y herramientas aisladas por un flujo unico y accionable. La ventaja competitiva es una fuente unica de verdad, rapida de consultar y suficientemente liviana de mantener para que el habito no se rompa.

**Vision:** Convertirse en la capa operativa del hogar — simple, veloz y mantenible, capaz de incorporar nuevos modulos sin aumentar complejidad operativa.

## Success Criteria

### User Success

- Encontrar cualquier informacion habitual en < 10 segundos.
- Localizar un objeto concreto en < 5 segundos.
- Tasa de exito al primer intento >= 90% en busqueda o navegacion simple.
- Reduccion de coordinacion verbal para ubicaciones y faltantes.

Senales de valor percibido: dejar de preguntarse donde esta algo, ir a comprar sin olvidar productos basicos, sentir el hogar ordenado sin carga mental adicional.

### Business Success

Objetivos a 3 meses: uso semanal constante por ambos usuarios, inventario base cargado y utilizable, lista de compra usada en contexto real, reduccion clara de olvidos y busquedas innecesarias.

Objetivos a 12 meses: sustitucion efectiva de notas y mensajes dispersos, consulta natural varias veces por semana, datos mantenidos actualizados con bajo esfuerzo, capacidad de incorporar nuevos modulos sin reescribir la base.

**North Star Metric MVP:** sesiones utiles por semana. Una sesion util es: encontrar algo, anadir o actualizar un item, marcar un producto faltante, o consultar la lista de compra.

### Technical Success

- Disponibilidad >= 99% mensual.
- Cero perdidas de datos confirmados por servidor.
- Inconsistencias menores < 1% y corregibles.
- Abrir app (LCP) <= 2 segundos en 4G.
- Buscar item <= 1 segundo.
- Marcar faltante o actualizar stock <= 500 ms percibidos.

### Measurable Outcomes

- Tiempo de consulta general < 10 s.
- Tiempo de localizacion de objeto < 5 s.
- Tasa de exito al primer intento >= 90%.
- Disponibilidad >= 99%.
- Incremento sostenido de sesiones utiles por semana.
- Evidencia de reduccion de olvidos y compras duplicadas en uso cotidiano.

## Product Scope

### Fase 1 — MVP

Alcance imprescindible para validar la propuesta de valor:

- Login seguro para dos usuarios.
- Inventario del hogar: CRUD con categoria, ubicacion y busqueda rapida.
- Gestion de consumibles: estado (en casa / faltante) y actualizacion rapida.
- Lista de compra generada desde faltantes, con marcado en un toque.
- Busqueda global rapida sobre inventario y consumibles.
- Experiencia responsive mobile-first (MPA).
- Persistencia fiable de datos en la nube.
- Visibilidad de estado de sincronizacion y reintento manual.

### Fase 2 — Post-MVP

Extensiones de valor una vez validado el nucleo:

- Biblioteca personal (libros, peliculas, series).
- Alertas de stock bajo.
- Fotos de objetos.
- Escaneo de codigo de barras.

### Fase 3 — Vision

Evolucion a plataforma domestica integral:

- Estadisticas de consumo.
- Gestion de tareas del hogar.
- Recetas, finanzas domesticas.
- Integracion con asistentes de voz.

## User Journeys

### Journey 1: Compra rapida en supermercado (happy path)

Alicia sale del trabajo, entra al supermercado con poco tiempo y abre Home App New. En segundos revisa faltantes y abre la lista de compra. Frente a un producto dudoso, usa busqueda rapida, confirma stock y evita compra duplicada. Termina la compra sin olvidos con sensacion de control.

Recuperacion ante fallo: si no hay conexion momentanea, trabaja sobre estado local reciente; al recuperar red sincroniza sin perdida.

Estado emocional: ansiedad → confianza operativa.

Capacidades reveladas: lista de compra accionable, busqueda sub-segundo, sincronizacion robusta, marcado con feedback inmediato.

### Journey 2: Conflicto de datos entre usuarios (caso limite)

Carlos ve un consumible como disponible, pero esta agotado. Ajusta la cantidad, marca faltante y anade contexto. El sistema detecta que Alicia edito el mismo item minutos antes, muestra el conflicto suave y propone la version mas reciente. Carlos resuelve en pocos toques.

Recuperacion ante fallo: conflicto repetido por ediciones simultaneas; historial corto y confirmacion explicita del valor final.

Estado emocional: frustracion → alivio y confianza en que el sistema no se rompe.

Capacidades reveladas: manejo de conflictos simple, historial breve por item, senales de ultima actualizacion, correccion rapida sin navegacion compleja.

### Journey 3: Mantenimiento semanal (usuario operativo)

Domingo por la tarde, Alicia dedica 10-15 minutos a revisar categorias principales (cocina, limpieza, bano), corregir cantidades y eliminar duplicados. Detecta patrones de faltantes recurrentes y prepara base de compra para la semana.

Recuperacion ante fallo: demasiados pasos; edicion en linea y atajos de actualizacion rapida.

Estado emocional: tarea pesada → rutina corta y util.

Capacidades reveladas: navegacion por categorias y ubicaciones, edicion rapida, UX de mantenimiento de baja friccion.

### Journey 4: Troubleshooting de sincronizacion (incidente de confianza)

Carlos no ve los cambios recientes de Alicia. Abre el panel de actividad reciente, identifica que tenia conectividad debil y fuerza sincronizacion. El incidente se resuelve sin salir de la app.

Recuperacion ante fallo: sync tarda mas de lo esperado; estado visible y boton de reintento.

Estado emocional: desconfianza → tranquilidad.

Capacidades reveladas: visibilidad de estado de sync, registro reciente entendible, reintento manual, mensajeria de error accionable.

### Journey 5: Integracion tecnica (Post-MVP)

En fase de crecimiento, una persona tecnica conecta un lector de codigos o API de retailer para reducir entrada manual. Al primer flujo exitoso, el hogar reduce tiempo de mantenimiento sin romper la experiencia base.

Recuperacion ante fallo: mapeo incorrecto de productos; capa de validacion y revision humana.

Estado emocional: cautela tecnica → validacion de escalabilidad.

Capacidades reveladas: API estable y versionada, mapeo de catalogo, observabilidad basica, fallback manual.

### Journey Requirements Summary

- Consulta ultrarrapida: busqueda, filtros simples, acceso en pocos toques.
- Operacion colaborativa confiable: sincronizacion, conflictos simples, estado de actualizacion visible.
- Mantenimiento sin friccion: edicion rapida, rutinas cortas, limpieza de duplicados.
- Resiliencia movil: feedback claro, reintentos, tolerancia a conectividad intermitente.
- Trazabilidad no tecnica: actividad reciente y cambios por item entendibles por usuario final.
- Escalabilidad post-MVP: API e integraciones sin degradar la experiencia core.

## Web App Specific Requirements

**Arquitectura:** MPA (Multi-Page Application). Cada modulo es una ruta independiente con recarga estandar. Sin SPA, sin PWA, sin service workers en MVP.

**Actualizacion de datos:** Sin tiempo real en MVP. La recarga de pagina es el mecanismo aceptado; elimina dependencias de WebSockets o SSE en esta fase.

**Persistencia:** API backend + base de datos cloud. Datos sincronizados via HTTP estandar.

### Browser Matrix

| Navegador | Version objetivo            |
|-----------|-----------------------------|
| Chrome    | Ultima estable              |
| Firefox   | Ultima estable              |
| Safari    | Ultima estable (iOS/macOS)  |
| Edge      | Ultima estable              |

Sin soporte para IE11 ni versiones con mas de un ciclo de actualizacion de antiguedad.

### Responsive Design

- Mobile-first: layout base para pantallas pequenas, adaptado a desktop.
- Breakpoints: movil (< 768 px), tablet/desktop (>= 768 px).
- Funcional y utilizable desde 320 px de ancho.

### SEO

No aplica. La app requiere autenticacion y no esta destinada a ser indexada por motores de busqueda.

### Autenticacion y Acceso

- Login seguro obligatorio antes de acceder a cualquier modulo.
- Dos usuarios por hogar; sin roles diferenciados en MVP.
- Sin integraciones externas en MVP (retailers, escaneo de codigos).

## Project Scoping

**Enfoque MVP:** MVP de experiencia — la experiencia minima que haga al usuario decir "esto es mas rapido que lo que usaba antes". El valor se valida por adopcion de habito, no por volumen de funcionalidades.

**Equipo estimado:** 1-2 personas full-stack capaces de cubrir frontend web, backend API y despliegue cloud.

### Risk Mitigation

**Riesgo de habito** (degradacion de datos por baja disciplina): UX de actualizacion en un toque como diseno central del MVP, no mejora posterior.

**Riesgo de mercado** (no sustituye herramientas informales): validar en primeras semanas si el tiempo de consulta supera la alternativa informal; ajustar flujo antes de anadir funcionalidades.

**Riesgo de scope creep**: congelar alcance MVP antes del desarrollo. Cualquier nueva idea entra en Fase 2 o Fase 3 sin negociacion durante sprint activo.

## Functional Requirements

### Autenticacion y Acceso

- FR1: Un usuario puede registrarse con credenciales propias.
- FR2: Un usuario puede iniciar sesion de forma segura.
- FR3: Un usuario puede cerrar sesion explicitamente.
- FR4: El sistema mantiene la sesion activa entre visitas sin reautenticacion constante.
- FR5: Dos usuarios acceden al mismo hogar con los mismos datos compartidos.

### Inventario del Hogar

- FR6: Un usuario puede anadir un objeto al inventario con nombre, categoria y ubicacion.
- FR7: Un usuario puede editar los datos de un objeto existente.
- FR8: Un usuario puede eliminar un objeto del inventario.
- FR9: Un usuario puede ver la lista completa de objetos del hogar.
- FR10: Un usuario puede navegar el inventario por categoria.
- FR11: Un usuario puede navegar el inventario por ubicacion.

### Consumibles

- FR12: Un usuario puede anadir un consumible con nombre y categoria.
- FR13: Un usuario puede marcar un consumible como "en casa" o "faltante".
- FR14: Un usuario puede editar los datos de un consumible existente.
- FR15: Un usuario puede eliminar un consumible.
- FR16: Un usuario puede ver todos los consumibles agrupados por estado.

### Lista de Compra

- FR17: El sistema genera la lista de compra a partir de los consumibles marcados como faltantes.
- FR18: Un usuario puede ver la lista de compra activa.
- FR19: Un usuario puede marcar un item de la lista como comprado.
- FR20: El sistema actualiza el estado del consumible al marcarlo como comprado.

### Busqueda

- FR21: Un usuario puede buscar objetos y consumibles mediante texto libre.
- FR22: Los resultados de busqueda cubren inventario y consumibles.
- FR23: Un usuario puede filtrar resultados por categoria o ubicacion.
- FR24: El sistema muestra un estado vacio util cuando no hay resultados.

### Sincronizacion y Colaboracion

- FR25: Los cambios de un usuario son visibles para el otro al recargar la pagina.
- FR26: El sistema detecta y muestra conflictos cuando dos usuarios editan el mismo item.
- FR27: Un usuario puede resolver un conflicto eligiendo que version conservar.
- FR28: Un usuario puede ver cuando y quien actualizo por ultima vez un item.

### Estado del Sistema

- FR29: El sistema muestra el estado de sincronizacion (sincronizado / pendiente / error).
- FR30: Un usuario puede forzar manualmente una sincronizacion.
- FR31: El sistema muestra un mensaje de error claro y accionable cuando falla una operacion.

### Navegacion y Experiencia

- FR32: Un usuario puede acceder a inventario, consumibles y lista de compra desde la navegacion principal.
- FR33: La aplicacion funciona correctamente en dispositivos moviles con pantallas pequenas.
- FR34: La aplicacion es accesible mediante teclado y lector de pantalla (WCAG 2.1 AA).
- FR35: Un usuario puede configurar la lista de compra como pantalla de inicio.

## Non-Functional Requirements

### Performance

- NFR1: LCP <= 2 segundos en red movil media (4G) en dispositivos de gama media.
- NFR2: Resultados de busqueda visibles <= 1 segundo tras input.
- NFR3: Acciones de marcado (faltante, comprado) con respuesta visual percibida <= 500 ms.
- NFR4: TTI <= 3 segundos en dispositivos de gama media.
- NFR5: Funcional y utilizable en pantallas de 320 px de ancho o mas.

### Security

- NFR6: Todas las comunicaciones sobre HTTPS con TLS 1.2 o superior.
- NFR7: Credenciales almacenadas con hash seguro (bcrypt o equivalente); nunca en texto plano.
- NFR8: Sesiones expiran tras periodo de inactividad configurable.
- NFR9: Datos de cada hogar accesibles solo por sus usuarios autorizados.
- NFR10: La aplicacion no expone datos de un hogar a usuarios de otros hogares en ningun escenario.
- NFR11: Tokens de sesion invalidados al cerrar sesion explicitamente.

### Accessibility

- NFR12: Cumplimiento WCAG 2.1 nivel AA en todas las paginas principales.
- NFR13: Contraste de texto >= 4.5:1 (texto normal) y >= 3:1 (texto grande).
- NFR14: Todos los elementos interactivos operables mediante teclado.
- NFR15: Compatible con VoiceOver (iOS/macOS), TalkBack (Android) y NVDA/JAWS (Windows).
- NFR16: Mensajes de error asociados programaticamente al elemento que los origina.

### Reliability

- NFR17: Disponibilidad >= 99% mensual (< 7.2 horas de caida al mes).
- NFR18: Sin perdida de datos confirmados por el servidor en ninguna circunstancia.
- NFR19: Operaciones fallidas muestran estado de error claro; nunca quedan en estado ambiguo.
- NFR20: En error de red, el sistema conserva el ultimo estado conocido y no muestra datos corruptos.
