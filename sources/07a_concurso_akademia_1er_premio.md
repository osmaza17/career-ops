# 1.er Premio — Hackathon Estudio de Caso, Programa Akademia — Fundación Innovación Bankinter (GENAQ)

**Tipo:** Concurso — consultoría estratégica en formato hackathon nacional competitivo
**Período:** septiembre 2024 – febrero 2025
**Cliente / Contexto:** GENAQ (fabricante del grupo Keyter, especializado en generación de agua atmosférica). Empresa pivotando de B2B consolidado (Departamento de Defensa de EE.UU., Acciona, Meliá) a mercado doméstico B2C/B2B con nuevo producto residencial (PVP 2.500 €, 10–12 L/día, vida útil 20 años).
**Marco competitivo:** Hackathon nacional del programa Akademia (Fundación Innovación Bankinter/UPV). 80 equipos competidores. Jurado ejecutivo externo.
**Equipo:** 6 personas
**Rol individual:** Líder del equipo. Responsable de la concepción del sistema de parámetros (arquitectura de la función multicriterio, selección y ponderación de los 7 criterios, lógica de normalización). Responsable de la presentación final ante el jurado (narrativa estratégica completa: del buyer persona a la recomendación de canal, pasando por el roadmap de producto). Coordinación de contribuciones individuales en torno a una tesis única. Trazabilidad entre input (datos públicos), modelo (función ponderada) y output (50 secciones priorizadas + recomendación accionable).

**Validación externa:** 🏆 1.er Premio entre 80 equipos · Jurado ejecutivo de la Fundación Bankinter

## Problema de negocio

GENAQ disponía de tecnología validada en segmento industrial pero carecía de estrategia de entrada para su nuevo producto residencial. Pregunta: "¿cómo lanzamos este MVP en España?" — formulación ambigua que requería traducción a problema operacionalizable.

**Entregables requeridos:** (1) Identificación geográfica del mercado piloto al máximo nivel de granularidad (sección censal, ~36.000 unidades en España) + (2) formulación de la estrategia comercial asociada.

## Metodología

**Modelo MCDA (Multi-Criteria Decision Analysis):** 7 dimensiones ponderadas para scoring de secciones censales:

1. Pirámide poblacional — 30%
2. Renta y consumo del hogar en categoría COICOP de electrodomésticos — 20%
3. Gasto en agua embotellada — 20%
4. Rendimiento y coste de generación según temperatura y humedad relativa (curvas propietarias del cliente) — 10%
5. Coste unitario del agua corriente — 10%
6. Densidad de población — 5%
7. Pluviometría — 5%

Pesos calibrados a partir del buyer persona definido previamente (30–50 años, ingreso medio-alto, entorno urbano/suburbano, sensibilidad ambiental).

**Fuentes de datos:**

- INE: renta media por hogar, encuesta de presupuestos familiares, suministro y saneamiento del agua
- AEMET: precipitación provincial
- Datos propietarios del cliente: curvas de generación-coste según condiciones ambientales

**Normalización:** 0-100 para combinar variables heterogéneas. Geo-visualización a nivel de sección censal sobre todo el territorio nacional.

**Capa adicional — Algoritmo K-Means no supervisado:** 5 clusters sobre las dos variables de mayor peso (renta y pirámide poblacional), diseñado como evolución del modelo para segmentar el mercado más allá del top-50 y soportar futuras iteraciones de marketing.

**Decisión de canal:** Análisis comparativo cualitativo con 5 criterios (experimentación previa a la compra, redes de distribución existentes, presencia online, cobertura nacional, servicio al cliente) sobre 3 alternativas: e-commerce propio, tienda física propia, grandes superficies. Resultado: distribuidores mayoristas en grandes superficies, con Leroy Merlin como partner prioritario por capacidad técnica de instalación.

**Herramienta entregable:** Modelo Excel automatizado y reutilizable con pesos editables por el cliente para regenerar resultados bajo distintos escenarios estratégicos.

## Resultados

- Lista priorizada de 50 secciones censales óptimas, top 10 identificado; concentración en el arco mediterráneo (Cataluña, Comunidad Valenciana) con nichos en Madrid, Aragón y zonas pirenaicas
- Recomendación de canal: grandes superficies, Leroy Merlin como partner prioritario
- Roadmap de mejoras de producto: IoT, agua caliente, agua con gas, integración fotovoltaica, rediseño estético
- Modelo paramétrico reutilizable entregado al cliente como herramienta operativa, no como informe estático
- 🏆 1.er Premio entre 80 equipos

## Competencias demostradas

_Analítica y técnica:_ Estructuración de problemas de negocio ambiguos · Modelado cuantitativo aplicado a decisiones estratégicas · Análisis multicriterio (MCDA) · Normalización y consolidación de fuentes heterogéneas · Geospatial analytics · Clustering no supervisado (K-Means) · Construcción de herramientas de decisión reutilizables en Excel

_Consultoría y negocio:_ Market entry strategy · Customer segmentation · Buyer persona · Go-to-market design · Análisis de canales con trade-off matrix · Roadmap de producto · Comunicación ejecutiva y data storytelling

_Liderazgo:_ Dirección de equipo multidisciplinar de 6 personas · Integración de contribuciones técnicas en narrativa única · Pitch ante jurado externo bajo presión competitiva · Gestión de plazos en formato hackathon
