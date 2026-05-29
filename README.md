# 🛢️ OilyGiant — Análisis de Riesgo y Selección de Regiones Petroleras
> Pipeline completo de Machine Learning para identificar la región óptima para
> perforar 200 nuevos pozos petroleros, maximizando el beneficio y minimizando
> el riesgo de pérdida mediante Regresión Lineal y análisis estadístico con Bootstrapping.

![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)
![Pandas](https://img.shields.io/badge/Pandas-2.x-150458?logo=pandas)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-1.x-F7931E?logo=scikit-learn)
![NumPy](https://img.shields.io/badge/NumPy-1.x-013243?logo=numpy)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter)

---

## 📌 Contexto del Negocio

La compañía OilyGiant necesita decidir en cuál de tres regiones geológicas abrir
200 nuevos pozos petroleros con un presupuesto de **$100,000,000 USD**. Una mala
decisión puede significar pérdidas millonarias. Este proyecto construye un modelo
predictivo del volumen de reservas por pozo y aplica análisis de riesgo estadístico
para recomendar la región con la mejor combinación de **ganancia esperada y riesgo
aceptable**, con un umbral máximo de pérdida del 2.5%.

---

## 🎯 Objetivo

- Construir un modelo predictivo del volumen de reservas por pozo.
- Seleccionar los **200 pozos con mayor potencial** estimado por región.
- Elegir la región con el mayor beneficio total mediante **análisis de riesgo con Bootstrapping**.

---

## 📁 Estructura del Proyecto

```
OilyGiant-Optimización-Pozos-Machine-Learning/
│
├── OilyGiant-Optimización-Pozos-Machine-Learning.ipynb  # Notebook principal
├── datasets/
│   ├── geo_data_0.csv    # Región 0
│   ├── geo_data_1.csv    # Región 1
│   └── geo_data_2.csv    # Región 2
└── README.md
```

---

## 📊 Dataset

Tres archivos CSV con datos de exploración geológica — **100,000 registros por región**.

| Columna | Tipo | Descripción |
|---------|------|-------------|
| `id` | object | Identificador único del pozo |
| `f0` | float64 | Característica geológica 0 |
| `f1` | float64 | Característica geológica 1 |
| `f2` | float64 | Característica geológica 2 |
| `product` ⭐ | float64 | Volumen de reservas en miles de barriles — Variable objetivo |

> `id` fue descartado como feature por no tener valor predictivo.

---

## 📋 Condiciones del Negocio

| Parámetro | Valor |
|-----------|-------|
| Puntos explorados por región | 500 |
| Pozos a desarrollar | 200 |
| Presupuesto total | $100,000,000 USD |
| Ingreso por unidad (miles de barriles) | $4,500 USD |
| Costo por pozo | $500,000 USD |
| Volumen mínimo por pozo (umbral) | 111.11 miles de barriles |
| Umbral de riesgo de pérdida | < 2.5% |
| Modelo permitido | Solo Regresión Lineal |

---

## ⚙️ Flujo de Trabajo

```
1. Exploración EDA
   ├── 300,000 registros — 0 nulos y 0 duplicados
   ├── Análisis de distribución de variable objetivo (product)
   └── Detección de particularidades por región (f2 discreta en Región 1)

2. Entrenamiento del Modelo — Regresión Lineal
   ├── Validación Cruzada K-Fold (k=5) para evaluar robustez
   ├── train_test_split final para generar predicciones del bootstrapping
   └── Comparativa de RMSE CV, Std y RMSE Relativo entre regiones

3. Umbral de Rentabilidad
   └── Comparación del volumen medio predicho vs umbral mínimo (111.11)

4. Cálculo de Ganancias — Top 200 Pozos
   └── Selección de los 200 pozos con mayor predicción y cálculo de ganancia real

5. Análisis de Riesgo — Bootstrapping (1,000 simulaciones)
   ├── Distribución de ganancias por región
   ├── Intervalo de confianza al 95%
   ├── Riesgo de pérdida (% de simulaciones con ganancia negativa)
   └── Evaluación del criterio < 2.5%
```

---

## 📈 Resultados del Modelo — Validación Cruzada K-Fold (k=5)

| Región | Volumen Medio Predicho | RMSE CV (medio) | RMSE Std | RMSE Relativo % |
|--------|------------------------|-----------------|----------|-----------------|
| Región 0 | 92.40 | 37.69 | 0.14 | 40.79% |
| **Región 1** ⭐ | 68.71 | **0.89** | **0.00** | **1.30%** |
| Región 2 | 94.77 | 40.06 | 0.08 | 42.27% |

> La Región 1 obtiene un RMSE CV de 0.89 con desviación estándar de 0.00 —
> consistente en los 5 folds, confirmando que la precisión no es producto
> de una partición afortunada sino de un patrón real y estable en los datos.

---

## 💰 Ganancia Potencial — Top 200 Pozos (Análisis Preliminar)

| Región | Ganancia Potencial |
|--------|--------------------|
| **Región 0** | **$33,591,411 USD** |
| Región 2 | $25,985,718 USD |
| Región 1 | $24,150,867 USD |

> ⚠️ Este análisis se basa en una única muestra fija sin considerar
> incertidumbre estadística. El Bootstrapping es el paso decisivo.

---

## 📊 Análisis de Riesgo — Bootstrapping (1,000 simulaciones)

| Región | Beneficio Promedio | IC 95% Inferior | IC 95% Superior | Riesgo Pérdida | Cumple <2.5% |
|--------|--------------------|-----------------|-----------------|----------------|--------------|
| Región 0 | $6,105,142 | -$164,652 | $12,594,164 | 2.90% | ❌ |
| **Región 1** ⭐ | **$6,454,391** | **$1,301,494** | **$11,736,387** | **0.50%** | **✅** |
| Región 2 | $5,956,272 | $399,317 | $12,091,551 | 1.10% | ✅ |

---

## 💡 Conclusiones Clave

- La **Región 1** es la recomendación de inversión más sólida, con el mayor
  beneficio promedio ($6,454,391), el menor riesgo de pérdida (0.50%, casi
  6 veces por debajo del umbral) y el intervalo de confianza inferior más alto
  ($1,301,494), garantizando ganancias incluso en escenarios adversos.

- La elección preliminar (Top 200 pozos) identificó a la **Región 0** como
  líder en ganancia puntual ($33,591,411). Sin embargo, el Bootstrapping
  reveló que esa ventaja era frágil: su riesgo de pérdida supera el umbral
  permitido y su IC toca territorio negativo.

- La precisión del modelo **importa más que el volumen medio regional**: aunque
  la Región 1 tiene el menor volumen medio (68.71), su RMSE CV de 0.89 produce
  selecciones de pozos más confiables y estables, lo que se traduce en menor
  riesgo de pérdida en el bootstrapping.

- La particularidad de `f2` en la Región 1 (valores casi discretos de 0 a 5)
  establece una relación lineal directa con `product` que la regresión lineal
  captura perfectamente — explicando el RMSE excepcionalmente bajo.

---

## 🛠️ Tecnologías Utilizadas

| Herramienta | Uso |
|-------------|-----|
| Python 3.x | Lenguaje principal |
| Pandas | Manipulación de datos |
| NumPy | Operaciones numéricas y Bootstrapping |
| Scikit-learn | Regresión Lineal, KFold, cross_val_score |
| Matplotlib / Seaborn | Visualización e histogramas de distribución |
| Jupyter Notebook | Entorno de desarrollo |

---

## 🚀 ¿Cómo ejecutar el proyecto?

```bash
# 1. Clona el repositorio
git clone https://github.com/tu_usuario/OilyGiant-Risk-Analysis-ML.git

# 2. Instala las dependencias
pip install pandas numpy scikit-learn matplotlib seaborn jupyter

# 3. Abre el notebook
jupyter notebook OilyGiant-Optimización-Pozos-Machine-Learning.ipynb
```

---

## 👤 Autor

**Juan Carlos Buri** — Junior Data Scientist | Geology & Mining Engineer

Proyecto desarrollado como parte del programa **Data Scientist — TripleTen Bootcamp 2026**
