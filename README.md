# 🤖 Predicción y Análisis de Rendimiento Fotovoltaico con SARIMAX

Este proyecto combina el modelado predictivo mediante series temporales y la simulación física de activos renovables. El objetivo es predecir la generación de potencia en la planta de Graus (Huesca) ajustando los datos teóricos a la realidad operativa.

## 1. Metodología y Ajuste Físico
Para que el modelo sea realista, se procesan los datos originales aplicando:
* **Degradación Térmica:** Reducción de eficiencia del 0.4% por cada grado sobre los 25°C.
* **Límite de Inversor:** Capado de potencia (clipping) a 1000 kW.

---

## 2. Entrenamiento e Inferencia

### Guía de Parámetros SARIMAX $(p, d, q) \times (P, D, Q, s)$

Para ajustar el modelo, debes modificar los valores dentro de `order=(p, d, q)` y `seasonal_order=(P, D, Q, s)`. Aquí tienes el desglose de qué significa cada uno y cómo tocarlos:

### 1. Componente No Estacional: `order=(p, d, q)`
Se encarga de la tendencia y las variaciones a corto plazo.

* **p (AR - Autoregresivo):** Número de retardos de la variable dependiente. Si p=1, el valor de esta hora depende directamente del valor de la hora anterior.
* **d (I - Integración):** Número de veces que hay que diferenciar los datos para hacerlos estacionarios. Si los datos tienen una tendencia clara, se usa d=1.
* **q (MA - Media Móvil):** Número de retardos del error de predicción. Ayuda a suavizar "shocks" o ruidos repentinos.

### 2. Componente Estacional: `seasonal_order=(P, D, Q, s)`
Es el que captura el ciclo diario de la radiación solar.

* **P (AR Estacional):** Similar a 'p', pero aplicado a ciclos completos (24h).
* **D (I Estacional):** Diferenciación aplicada al ciclo estacional.
* **Q (MA Estacional):** Media móvil aplicada a los errores del ciclo estacional.
* **s (Periodicidad):** El número de pasos de tiempo que forman un ciclo. 
    * **s=24:** Fundamental para datos horarios (ciclo de un día).

---

### ¿Cómo saber qué valores poner? (Resumen rápido)

| Si ves que... | Acción recomendada |
| :--- | :--- |
| Los datos suben/bajan a largo plazo | Incrementa **d** a 1. |
| El gráfico ACF tiene picos cada 24 horas | Mantén **s=24** y ajusta **P** o **Q**. |
| El modelo tarda mucho y no converge | Reduce los valores (máximo 2). |
| Hay mucho ruido en la salida | Incrementa **q** o **Q** para suavizar. |

> **Nota:** En modelos de energía solar, la configuración `(1, 1, 1)x(1, 1, 1, 24)` es un estándar sólido porque asume que lo que pasó hace una hora y lo que pasó ayer a la misma hora son tus mejores predictores.

---

## 🛠️ Requisitos e Instalación
Para replicar este análisis, instala las dependencias necesarias:

```bash
pip install -r requirements.txt