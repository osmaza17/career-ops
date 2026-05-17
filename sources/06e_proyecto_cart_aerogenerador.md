# Regulación de potencia de aerogenerador CART — Enseignement d'Intégration Systèmes Multi-Énergie, CentraleSupélec

**Tipo:** Proyecto académico — modelado, control óptimo y análisis energético
**Período:** noviembre 2025
**Cliente / Contexto:** Asignatura Enseignement d'Intégration: Systèmes Multi-Énergie de CentraleSupélec. Turbina de referencia: CART (Controlled Advanced Research Turbine), descrita en literatura científica de L2S/CentraleSupélec y EDF R&D.
**Equipo:** 5 personas — Óscar Martínez Zamora (primero en la portada, en lista no ordenada alfabéticamente), Aziz Kaboré, Isabela Corrêa Hillal, Rafael Kenji Teramoto, Beatriz Brioli Doll de Moraes
**Rol individual:** Primer nombre en la portada del equipo de 5 (lista no ordenada alfabéticamente).

## Alcance técnico del proyecto

El proyecto cubre el ciclo completo de ingeniería de control de un aerogenerador CART:

**1. Modelado no lineal:** Sistema de 3 ecuaciones diferenciales acopladas (dinámica del rotor, del generador y de la torsión del eje). Coeficiente de potencia Cp(λ, θ) según modelo aerodinámico de la turbina CART.

**2. Análisis cualitativo de estabilidad:** Estudio del signo de ∂Tr/∂ωr en el entorno de los puntos de equilibrio. Distinción de zonas estables (el sistema absorbe perturbaciones) de zonas inestables (el sistema las amplifica). Resultado clave: el modelo no lineal sin control se detiene completamente con vientos bajos por entrada en zona inestable — fenómeno que el modelo lineal no reproduce por construcción (identificación honesta y bien interpretada de comportamiento no trivial).

**3. Linealización:** Desarrollo de Taylor de primer orden alrededor del punto de equilibrio óptimo (ωr,opt = 3,05 rad/s, λopt = 9,2, θ = 5°). Cálculo simbólico de jacobianos → matrices A, B, Bd, C, D en representación de estado.
**Protocolo de validación original:** Aplicación de escalones de couple de amplitudes variables (de 10⁻⁶ a 10² N·m) y verificación de que el error de modelado escala cuadráticamente con la amplitud (E ∝ Δũ²). Pendiente 2 en escala log-log = prueba experimental de que el modelo está bien construido.

**4. Diseño de controlador LQI (Linear Quadratic Integral):** Regulación cuadrática lineal con acción integral sobre el error de velocidad rotórica. Matrices de ponderación QLQI y RLQI ajustadas iterativamente para cumplir: sobreimpulso < 10%, tiempo de respuesta < 20 s, error estático nulo. Gain K obtenido resolviendo la ecuación algebraica de Riccati asociada mediante función `lqr` de MATLAB. Controlador resultante: K = 10³·[−2,18; −0,041; 0,407; −0,548].

**5. Optimización numérica de parques eólicos en cascada:** Modelado del efecto de estela según teoría de momentum de Betz. Corrección crítica identificada por el equipo: la formulación inicial vsiguiente = v(1−α) genera rendimientos no físicos (>100% para 7+ turbinas); sustituida por vout = vin(1−2α), que respeta la conservación de la energía. Optimización del vector de factores de inducción {αi} con `fmincon` de MATLAB para 2, 3 y 4 turbinas.

## Resultados clave

- Identificación del punto de operación absolutamente óptimo: Cp,max = 0,4655 con λopt = 8,14 y θopt = 0° (frente a Cp,max = 0,3476 con la restricción θ = 5° del enunciado)
- El control LQI **duplica** la energía extraída respecto a la operación sin control (32,1% del potencial Betz vs. 16,0%), con perfil de viento real (wind7.mat)
- Rendimientos globales de cascadas: 2 turbinas = 64,00% · 3 turbinas = 65,31% · 4 turbinas = 65,84%
- Análisis físico de retornos marginales decrecientes en cascadas

## Episodios clave

1. **Autocorrección del modelo de cascada:** el equipo detecta que su formulación viola la conservación de la energía (rendimiento >100% con 7+ turbinas), diagnostica la causa y corrige. Secuencia clásica de consultor: pensamiento crítico → diagnóstico → corrección con marco teórico.
2. **Interpretación del fenómeno de detención del modelo no lineal:** el aparente "error" numérico es la manifestación correcta de la zona inestable predicha en la sección 2. Capacidad de conectar resultados numéricos con teoría física.
3. **Validación experimental cuadrática del error de linealización:** diseñar un experimento que confirma una predicción teórica de orden de magnitud. Metodología científica de manual.

## Competencias demostradas

_Técnica y científica:_ Modelado no lineal de sistemas dinámicos multivariable · Análisis cualitativo de estabilidad · Linealización por Taylor con validación experimental rigurosa · Control óptimo (LQR/LQI) · Tuning de matrices de ponderación frente a especificaciones cuantitativas · Optimización numérica con restricciones (fmincon) · Simulación en MATLAB/Simulink nivel avanzado · Análisis energético comparativo con marco teórico explícito (límite de Betz, teorema de momentum)

_Razonamiento técnico transferible a consultoría:_ Identificación y corrección de errores de modelado mediante consistencia física · Interpretación física de fenómenos numéricos contraintuitivos · Articulación de trade-offs entre escala y rendimiento marginal en sistemas multi-componente · Dominio del lenguaje técnico de transición energética y energías renovables

> **[META-INSTRUCCIÓN]** Los tres episodios clave pueden explotarse de forma independiente en entrevistas, con independencia del rol exacto asumido en el proyecto. Son especialmente pertinentes para demostrar autocorrección, interpretación física de fenómenos numéricos y metodología científica rigurosa.
