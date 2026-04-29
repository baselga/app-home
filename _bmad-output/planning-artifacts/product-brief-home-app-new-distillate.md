---
title: "Product Brief Distillate: home-app-new"
type: llm-distillate
source: "product-brief-home-app-new.md"
created: "2026-04-28T23:46:01.4134497+02:00"
purpose: "Token-efficient context for downstream PRD creation"
---

# Product Brief Distillate: Home App New

## Resumen del Producto

- Home App New es una app web responsive de uso personal y familiar para gestion integral del hogar, con foco inicial en inventario domestico y consumibles con lista de compra.
- El producto apunta a resolver una friccion cotidiana de alta frecuencia: saber que hay en casa y que hace falta comprar sin depender de varias apps, notas o memoria.
- El contexto de uso principal es un hogar de dos personas adultas con acceso completo a los mismos datos, sin necesidad de roles complejos en la primera fase.
- La promesa principal del MVP es simplicidad extrema y rapidez real de uso, especialmente en movil.

## Problema y Escenario de Uso

- El dolor principal es la fragmentacion: hoy estas tareas se reparten entre notas, mensajes, memoria y apps distintas, lo que genera olvidos, compras duplicadas y baja confianza en la informacion.
- El momento de valor mas importante es consultar que hace falta comprar en menos de 10 segundos al pasar por el supermercado.
- Otro escenario importante es localizar rapidamente objetos del hogar por categoria y ubicacion sin tener que preguntar o buscar fisicamente.
- El producto debe ser mas rapido que la alternativa informal; si introducir o consultar datos cuesta demasiado, el habito se rompe.

## Usuarios y Acceso

- Usuarios iniciales: dos adultos del mismo hogar.
- Ambos usuarios tienen acceso completo a toda la informacion.
- No se necesita sistema de roles, permisos granulares ni multi-hogar en MVP.
- La experiencia debe estar optimizada para coordinacion simple entre dos personas, no para estructuras familiares complejas.

## Alcance MVP

- Modulo de inventario del hogar con CRUD, categorias, ubicaciones y busqueda rapida.
- Modulo de consumibles con control simple del estado y generacion de lista de compra clara.
- Persistencia de datos en la nube para dos usuarios.
- Experiencia responsive mobile-first con interfaz extremadamente simple.
- Arquitectura modular para permitir crecimiento posterior sin reescribir la base del producto.

## Fuera de Alcance MVP

- Biblioteca personal pasa a post-MVP aunque era una idea original del producto; se recorta para proteger foco y velocidad.
- Automatizaciones complejas quedan fuera del MVP.
- Roles avanzados y permisos granulares quedan fuera del MVP.
- Soporte multi-hogar queda fuera del MVP.
- Integraciones avanzadas, IA sofisticada, OCR o automatizaciones tipo smart home no forman parte de la primera version.

## Rationale de Priorizacion

- Inventario del hogar y consumibles impactan mas directamente la operativa diaria y ofrecen valor inmediato y frecuente.
- Biblioteca personal aporta valor, pero no con la misma urgencia funcional en el dia a dia del hogar.
- El criterio de priorizacion es: valor cotidiano alto, friccion baja, complejidad contenida.
- El MVP debe sentirse util muy pronto, sin una fase larga de configuracion o carga inicial.

## Requisitos y Senales de Producto

- Mobile first es una prioridad explicita, no un ajuste posterior.
- La interfaz debe ser extremadamente simple y de navegacion rapida.
- Buen rendimiento es requisito central, especialmente en tareas cortas y frecuentes.
- La persistencia de datos debe generar confianza: la informacion tiene que sentirse siempre disponible y consistente.
- El sistema debe escalar por modulos, no como una app monolitica cerrada.

## Metricas de Exito Elegidas

- Ver la lista de compra util en menos de 10 segundos en contexto movil.
- Uso semanal recurrente entre los dos usuarios.
- Utilidad real percibida: reduccion de olvidos y compras duplicadas en tareas cotidianas.

## Riesgos Clave

- Riesgo de abandono por friccion manual: si actualizar inventario o consumibles es pesado, el sistema perdera valor rapido.
- Riesgo de confianza de datos: si la nube o sincronizacion fallan, el producto pierde credibilidad frente a notas o mensajes.
- Riesgo de scope creep: la vision de hub domestico completo puede contaminar decisiones del MVP si no se mantiene disciplina de alcance.
- Riesgo de que el problema principal no sea inventario sino compra; el MVP debe validar rapidamente cual es el motor real de valor.

## Decisiones de Posicionamiento

- El foco de posicionamiento debe mantenerse en simplicidad y rapidez, no en privacidad ni automatizacion avanzada.
- La diferenciacion se basa en ser una unica app domestica simple para tareas reales de alta frecuencia, no en una tecnologia exclusiva.
- La narrativa recomendada es utilidad inmediata para coordinacion del hogar, no ambicion de plataforma desde el dia uno.

## Hallazgos de Mercado y Competencia

- El mercado esta fragmentado: hay apps separadas para listas de compra, gestion de despensa, inventario del hogar y biblioteca personal, pero casi no hay experiencias integradas y simples.
- Alternativas como Bring destacan en lista de compra compartida, pero no cubren inventario del hogar ni un flujo unificado.
- Soluciones de despensa con IA o recetas tienden a ser mas complejas o verticales de cocina que de gestion integral del hogar.
- Apps de biblioteca como Goodreads o StoryGraph resuelven seguimiento lector, pero no tienen relacion operativa con hogar; esto refuerza mover biblioteca a post-MVP.
- La oportunidad detectada no es competir por cantidad de funciones, sino por integracion y baja friccion.

## Oportunidades Post-MVP

- Biblioteca personal como tercer modulo una vez estabilizados inventario y compra.
- Evolucion a hub domestico completo para organizar cualquier tarea de la casa.
- Posibles modulos futuros: tareas del hogar, mantenimiento, calendarios o automatizaciones selectivas.
- Integraciones con retailers, smart home o analitica de consumo son oportunidades futuras, no necesidades iniciales.

## Preguntas Abiertas para PRD

- Como se implementara exactamente la persistencia en la nube y la sincronizacion para dos usuarios.
- Que modelo de datos minimo necesitan inventario y consumibles para no sobrecargar UX.
- Cual es el onboarding minimo para que la primera sesion entregue valor rapido.
- Que reglas simples convierten el estado de consumibles en una lista de compra util.
- Como se validara en las primeras semanas si el mayor valor esta en inventario, compra o la combinacion de ambos.