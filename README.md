<img width="1280" height="640" alt="WhatsApp Image 2026-11-17 at 3 00 46 PM (11)" src="https://github.com/user-attachments/assets/afbbbf71-59f1-4455-a57a-a723771f8cea" />

# ⛏️ Μ Proyecto Mu | Predicción de Recuperación de Oro en Procesos Mineros (Zyfra)

## 📋 Descripción
La industria minera requiere procesos altamente eficientes para maximizar la recuperación de metales valiosos y minimizar pérdidas operativas. **Zyfra**, empresa desarrolladora de soluciones tecnológicas para la industria pesada, necesita anticipar el rendimiento de extracción de oro durante el procesamiento del mineral.

Este proyecto desarrolla un modelo de Machine Learning capaz de predecir la recuperación de oro en dos etapas críticas del proceso metalúrgico:
* **Recuperación del concentrado rougher** (`rougher.output.recovery`).
* **Recuperación final** tras las etapas de purificación (`final.output.recovery`).

## 🎯 Objetivos
- ✅ Validar matemáticamente el cálculo de recuperación *rougher* y auditar la integridad de los conjuntos de entrenamiento y prueba.
- ✅ Identificar y excluir las 34 variables posteriores al proceso (*output* y *calculation*) ausentes en prueba para evitar fuga de datos (*data leakage*).
- ✅ Analizar la evolución de las concentraciones de **Oro (Au)**, **Plata (Ag)** y **Plomo (Pb)** a lo largo de las etapas de flotación y purificación.
- ✅ Depurar anomalías en concentraciones totales cercanas a cero e imputar valores faltantes respetando la secuencia temporal (`ffill()`).
- ✅ Implementar la métrica **sMAPE** y el **sMAPE final ponderado**, entrenando y validando modelos de regresión para superar la línea base.

## 🛠️ Herramientas y Habilidades Utilizadas
* **Lenguaje:** Python
* **Librerías:** Pandas, NumPy, Scikit-Learn, Matplotlib y Seaborn
* **Entorno:** Jupyter Notebook / Google Colab (`Proyecto_Mu_Prediccion_de_Recuperacion_de_Oro.ipynb`)
* **Técnicas:** Validación Cruzada Temporal (5 bloques), Imputación Temporal (`ffill`), Detección y Limpieza de Anomalías, Regresión Multi-Objetivo y Métrica Personalizada (sMAPE).

## 📊 Fuente de Datos y Estructura
El proyecto utiliza tres archivos históricos con registros cronológicos (`date`):
* **`gold_recovery_train.csv`**: Conjunto de entrenamiento (**13.789 observaciones** válidas tras limpieza).
* **`gold_recovery_test.csv`**: Conjunto de prueba (**5.154 observaciones** válidas y **52 características** predictoras).
* **`gold_recovery_full.csv`**: Conjunto de datos fuente completo.

Los atributos siguen la nomenclatura `[etapa].[tipo_parametro].[nombre_parametro]` abarcando las etapas de alimentación inicial, flotación (`rougher`), purificación primaria (`primary_cleaner`), purificación secundaria (`secondary_cleaner`) y concentrado final (`final`).

## ⚙️ Metodología
1. **Preparación y Validación:** Verificación de la fórmula teórica de recuperación con un MAE de **$9.30 \times 10^{-15}$**, eliminación de registros sin objetivos e imputación cronológica hacia adelante (`ffill()`).
2. **Análisis Exploratorio (EDA):** Seguimiento de la concentración de metales por etapa, comparación de distribuciones del tamaño de partícula (`rougher.input.feed_size`) y remoción coordinada de **2.814 registros anómalos** (12,39% del dataset) con concentraciones totales $\le 1$.
3. **Modelado y Validación Cruzada:** Implementación de las métricas personalizadas **sMAPE** y **sMAPE ponderado** ($0.25 \times \text{sMAPE}_{\text{rougher}} + 0.75 \times \text{sMAPE}_{\text{final}}$), evaluando **Regresión Lineal** y **Bosque Aleatorio** mediante validación cruzada de 5 bloques.
4. **Evaluación Final:** Entrenamiento del modelo ganador con el conjunto de entrenamiento completo y comparación contra un modelo de referencia (*DummyRegressor*).

## 💡 Resultados y Métricas

### Evolución de Concentraciones Medias por Etapa
| Etapa del Proceso | Oro (Au) | Plata (Ag) | Plomo (Pb) |
| :--- | :---: | :---: | :---: |
| **Alimentación Inicial** | 7.55 | 8.04 | 3.28 |
| **Concentrado Rougher** | 17.83 | 10.85 | 6.89 |
| **Purificación Primaria** | 29.12 | 7.67 | 8.81 |
| **Concentrado Final** | **39.89** | 4.77 | 9.07 |

### Validación Cruzada (5 Bloques)
| Modelo Evaluado | Hiperparámetros Óptimos | sMAPE Promedio (Validación) |
| :--- | :--- | :---: |
| **Bosque Aleatorio (Random Forest)** | `n_estimators=150`, `max_depth=3` | **8.77%** |
| **Regresión Lineal** | Por defecto | 10.01% |

### 🏆 Evaluación Final en el Conjunto de Prueba
| Métrica | Bosque Aleatorio | Modelo de Referencia |
| :--- | :---: | :---: |
| **sMAPE Rougher (25%)** | **4.99%** | — |
| **sMAPE Recuperación Final (75%)** | **7.74%** | — |
| **sMAPE Ponderado Final** | **7.06%** | 7.77% |

## 📈 Principales Hallazgos
* **Enriquecimiento del mineral:** La concentración media de oro crece de forma continua desde **7.55** en la alimentación hasta **39.89** en el concentrado final, confirmando la eficacia física del circuito de flotación y limpieza.
* **Control de sobreajuste:** Árboles con poca profundidad (`max_depth=3`) lograron la mejor generalización, evitando el sobreajuste ante el ruido industrial del proceso.
* **Superación de la línea base:** El **Bosque Aleatorio** redujo el **sMAPE ponderado final a 7.06%**, superando al modelo de referencia (`7.77%`) por **0.71 puntos porcentuales** (una mejora relativa del **9.2%**).

## 📂 Archivos Principales
* `Proyecto_Mu_Prediccion_de_Recuperacion_de_Oro.ipynb`
* `gold_recovery_train.7z` / `gold_recovery_test.csv` / `gold_recovery_full.7z`

## 🏁 Conclusión
Se desarrolló un sistema predictivo robusto basado en **Bosque Aleatorio** capaz de estimar la recuperación de oro con un **sMAPE ponderado final de 7.06%**. La solución proporciona a **Zyfra** una herramienta confiable para anticipar el rendimiento metalúrgico, detectar desviaciones operativas y optimizar el uso de reactivos en planta.
