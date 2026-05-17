# Misión de consultoría de operaciones H2E Supply Chain — Air Liquide / ST7 Optimisation et gestion de flux, CentraleSupélec

**Tipo:** Proyecto — consultoría de operaciones con cliente industrial real
**Período:** abril 2026
**Cliente / Contexto:** Air Liquide (líder mundial en gases industriales). Marco: asignatura ST7 de CentraleSupélec con interlocución directa con el cliente. Dominio: mobilité hydrogène / cadena de suministro de hidrógeno en modo swap.
**Equipo:** internacional multicultural (Brasil, China, Portugal, España)
**Rol individual:** Líder técnico del proyecto. Responsable del desarrollo de la heurística de despacho (componente algorítmico más diferenciado) y de la construcción del fichero base de simulación en Simul8. Contribución transversal a: diseño de experimentos, razonamiento económico de recomendaciones, redacción del informe, preparación y entrega de la presentación final ante Air Liquide.

## Problema planteado por el cliente

Red de distribución en estrella: un Centro de Llenado (FC) con 3 rampas paralelas abastece 5 Estaciones de Repostaje de Hidrógeno (HRS) a distancias de 50–200 km, con tasas de consumo heterogéneas (10–50 kg/h) y operativa de 20h/día. Operación en modo swap. Restricciones: turnos de conductores (05:00–13:00 y 13:00–21:00), ventana nocturna sin despachos, prohibición dominical de entregas.

**Pregunta central del cliente:** ¿Cuál es la configuración mínima viable de flota, conductores y capacidad de almacenamiento que garantiza cero roturas de stock?

## Metodología y herramientas

**Modelo de simulación de eventos discretos (Simul8):** Cada remolque transita entre estados: parking vacío → rampa de llenado → parking lleno → tránsito ida → swap en HRS → tránsito vuelta. Recursos limitantes: operador del FC (24/7) y pool de conductores (turnos). Componentes estocásticos: tiempos de conexión/desconexión Uniform[3,8] min y tiempos de tránsito Normal con σ = 10% de la media.

**Heurística de despacho reactiva y greedy (diseño propio del equipo, contribución de Óscar):** En cada ciclo de evaluación, el algoritmo calcula una función de coste compuesta para cada estación elegible:

- Coste de tránsito (tarifa de conductor × tiempo)
- Penalización por ineficiencia (kg no absorbibles porque el tanque sigue demasiado lleno, valorados al precio de mercado del H₂)
- Penalización por horas extra (si el retorno excede el fin de turno)
- Componente de ruptura (bonus fuertemente negativo cuando el nivel proyectado en llegada cae bajo el margen de seguridad dinámico; penalización de superávit que desincentiva entregas prematuras)
- Margen de seguridad dinámico según turno y día: el turno nocturno del sábado incorpora un suplemento de 24h para garantizar supervivencia del domingo bajo prohibición de entregas

Esta heurística es propietaria del equipo y trasciende el uso de funcionalidades preconfiguradas de Simul8.

**Protocolo de validación (buenas prácticas de simulación):**

- Período de calentamiento: determinado por método gráfico con filtro de media móvil (k=10); fijado conservadoramente en 60 horas
- Número de réplicas: calculado a IC 95% mediante calculadora de Simul8 (4 réplicas)
- Validación white-box: seguimiento manual de remolques, verificación de niveles de H₂, prueba de condiciones extremas (N=0/M=0 vs. N=50/M=50), balance de masa en múltiples instantes

**Experimentación:** Reducción secuencial de recursos desde configuración base hasta mínimo viable en 3 escenarios (HRS tanque único, HRS multi-tanque, restricción dominical). Análisis de sensibilidad sobre 5 escenarios.

## Entregables al cliente

- Informe técnico estructurado (metodología, validación, experimentación)
- Modelo Simul8 reutilizable
- Tablas de configuraciones óptimas y análisis de sensibilidad con intervalos de confianza
- Recomendación final argumentada económicamente

## Competencias demostradas

_Técnica y cuantitativa:_ Simulación de eventos discretos · Diseño de heurísticas de despacho propietarias con función de coste multi-componente · Modelado estocástico de cadenas de suministro logísticas · Validación estadística de modelos (warm-up, réplicas, IC 95%, white-box) · Análisis de sensibilidad sistemático · Dimensionado de flota y recursos · Visual Logic (Simul8)

_Operaciones y consultoría:_ Fleet dimensioning · Optimización de cadenas de suministro logísticas · Gestión de restricciones operativas complejas (turnos, ventanas temporales, restricciones regulatorias) · Análisis coste-beneficio de configuraciones alternativas · Formulación de KPIs operativos acordados con cliente · Bottleneck dynamics · Recomendaciones accionables con justificación económica explícita

_Cliente y soft:_ Interacción directa con Air Liquide en dominio estratégico (transición energética / H₂) · Reuniones intermedias de alineación · Presentación final de resultados al cliente · Liderazgo técnico individualizable dentro del equipo · Redacción de informe técnico orientado a decisor industrial · Trabajo en equipo internacional multicultural
