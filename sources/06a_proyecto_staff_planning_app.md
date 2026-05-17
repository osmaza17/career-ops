# Aplicación de optimización de staff planning para asociaciones — Pôle Projet Associatif Numérique, CentraleSupélec

**Tipo:** Proyecto de innovación — modelado matemático, desarrollo de software, transformación digital
**Período:** diciembre 2024 – en curso
**Cliente / Contexto:** Pôle Projets Associatifs Numériques de CentraleSupélec. Proyecto autogenerado, admitido como proyecto oficial del semestre. Primera usuaria de producción: Lisa Devaux (Secretaria General del BDI en el ski de invierno del BDI).
**Equipo:** Individual (proyecto en solitario)
**Rol individual:** Concepción, formulación matemática, desarrollo, user research, iteración y coordinación con la responsable del Pôle Projet (Jennifer) para la puesta en producción

## Problema de negocio identificado

El staff planning es uno de los procesos operativos más tediosos y costosos en las asociaciones estudiantiles de CentraleSupélec. El proceso heredado: encuesta (LettuceMeet/Google Forms/WhatsApp) → Excel → resolución manual. Coste: entre 1 hora y decenas de horas de trabajo activo por evento, con resultados frecuentemente subóptimos (distribuciones inequitativas, asignaciones no deseadas, ausencia de descansos estructurados).

## Formulación matemática del modelo (MILP)

Programa lineal entero mixto (MILP) multi-objetivo sobre cuatro índices: personas × tareas × horas × días. El modelo incorpora un conjunto amplio de parámetros operativos — disponibilidad (dura y blanda), demanda por tarea, competencias, preferencias, restricciones de rotación y vínculos sociales — junto con una variable binaria principal de asignación y un conjunto de variables auxiliares para penalizar violaciones de restricciones con distinta severidad. La función objetivo minimiza una suma ponderada con jerarquía explícita, priorizando cobertura de demanda y mandatos del responsable sobre equidad, preferencias individuales y continuidad de turno.

## User research y metodología de desarrollo

- Más de 20 entrevistas semidirigidas con secretarios generales de asociaciones de CentraleSupélec: BDI, BDA, BDE, WACS, RAID, TOSS, PICS, Huma, NCV, Forum, Sympo, AdR, Capese, Genius, Nepal, OSER, entre otros
- Objetivo: mapear la diversidad de reglas operativas de cada asociación y traducirlas a nuevas restricciones formales del modelo
- Backlog activo con priorización must-have/nice-to-have, refactorización iterativa, corrección de bugs, optimización de uso de RAM

## Decisión metodológica sobre AI-assisted development

La formulación matemática y el diseño del modelo son aportación humana íntegra. La implementación de la interfaz Python y la lógica de aplicación se aceleraron mediante Claude Code y herramientas equivalentes de desarrollo asistido por IA. Esta decisión eliminó meses de aprendizaje en front-end que no eran el cuello de botella intelectual del proyecto. Es un caso concreto y cuantificable de AI-augmented engineering: criterio humano sobre el "qué" y el "por qué", aceleración asistida por IA sobre el "cómo".

## Resultados y entregables

- Reducción del tiempo de planificación: de 1–10 horas de trabajo manual a segundos–minutos de cómputo automatizado (reducción de ~2 órdenes de magnitud)
- Aplicación funcional desplegada y validada en uso real
- Modelo matemático documentado y reutilizable
- Más de 20 entrevistas a stakeholders convertidas en restricciones operativas configurables
- Hoja de ruta activa con must-haves y nice-haves priorizados
- Adopción comprometida por la totalidad de las asociaciones de CentraleSupélec una vez completado el despliegue online
- Proyectos derivados ya identificados: modelos específicos para RAID y Forum; algoritmo de matching para el programa de mentoring del BDI

## Competencias demostradas

_Técnica y cuantitativa:_ Investigación operativa aplicada · Mixed-integer linear programming · Modelado matemático multi-objetivo · Linearización de restricciones no convexas · Solver comercial Gurobi · Arquitectura de aplicaciones web · Optimización de recursos computacionales (RAM, warm starts)

_Transformación digital y AI-assisted engineering:_ Diseño de workflows híbridos humano-IA en proyecto real con usuarios finales · Descomposición del problema entre componentes que requieren juicio humano y componentes susceptibles de delegación a IA · Criterio para validar output de IA contra especificación formal previa · Caso cuantificable de uso de IA para multiplicar productividad individual

_Producto y consultoría:_ User research estructurado a escala (20+ entrevistas) · Traducción de requisitos ambiguos a especificaciones formales · Stakeholder management con perfiles heterogéneos · Identificación de oportunidades de transformación digital en procesos manuales aceptados como inevitables

_Ejecución y autonomía:_ Iniciativa propia (proyecto autogenerado, no asignado) · Entrega sostenida bajo restricciones de tiempo no remuneradas
