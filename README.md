<img width="1280" height="640" alt="WhatsApp Image 2026-11-17 at 3 00 46 PM (11)" src="https://github.com/user-attachments/assets/afbbbf71-59f1-4455-a57a-a723771f8cea" />

# ⛏️ Μ Proyecto Mu | Predicción de Recuperación de Oro en Procesos Mineros (Zyfra)

## 📋 Descripción
La industria minera requiere procesos eficientes para maximizar la recuperación de metales valiosos y reducir pérdidas operativas. **Zyfra**, empresa desarrolladora de soluciones tecnológicas para la industria pesada, necesita anticipar el rendimiento de extracción de oro durante el procesamiento del mineral.

Este proyecto desarrolla un modelo de Machine Learning capaz de predecir la recuperación de oro en dos etapas críticas del proceso metalúrgico:
* **Recuperación del concentrado rougher** (`rougher.output.recovery`).
* **Recuperación final** tras las etapas de purificación (`final.output.recovery`).

## 🎯 Objetivos
- ✅ Validar matemáticamente el cálculo de recuperación *rougher* y auditar la integridad de los conjuntos de entrenamiento y prueba.
- ✅ Identificar y excluir las **34 variables** posteriores al proceso (*output* y *calculation*) ausentes en prueba para evitar fuga de datos (*data leakage*).
- ✅ Analizar la evolución de las concentraciones de **Oro (Au)**, **Plata (Ag)** y **Plomo (Pb)** a lo largo de las etapas de flotación y purificación.
- ✅ Depurar anomalías con concentraciones totales iguales a cero e imputar valores faltantes respetando la secuencia temporal (`ffill()`).
- ✅ Implementar las métricas **sMAPE** y **sMAPE final ponderado**, entrenando y validando modelos de regresión mediante validación cruzada.

## 🛠️ Herramientas y Habilidades Utilizadas
* **Lenguaje:** Python
* **Librerías:** Pandas, NumPy, Scikit-Learn y Matplotlib
* **Entorno:** Jupyter Notebook (`Proyecto_Mu_Prediccion_de_Recuperacion_de_Oro.ipynb`)
* **Técnicas:** Validación Cruzada (`KFold` de 5 pliegues), Imputación Temporal (`ffill`), Detección y Limpieza de Anomalías, Regresión Multi-Objetivo y Métrica Personalizada (sMAPE).

## 📊 Fuente de Datos y Estructura
El proyecto utiliza tres archivos históricos con registros cronológicos (`date`):
* **`gold_recovery_train.csv`**: Conjunto de entrenamiento (`data_1`).
* **`gold_recovery_test.csv`**: Conjunto de prueba (`data_2`) con **52 características** predictoras.
* **`gold_recovery_full.csv`**: Conjunto de datos fuente completo (`data_3`, con **20.420 registros válidos** tras la limpieza de anomalías).

Los atributos siguen la nomenclatura `[etapa].[tipo_parametro].[nombre_parametro]` abarcando las etapas de alimentación inicial, flotación (`rougher`), purificación primaria (`primary_cleaner`), purificación secundaria (`secondary_cleaner`) y concentrado final (`final`).

## ⚙️ Metodología
1. **Preparación y Validación:** Verificación de la fórmula teórica de recuperación con un Error Absoluto Medio (MAE) de **$9.44 \times 10^{-15}$**, indexación cronológica por `date` e imputación hacia adelante (`ffill()`) resguardando los valores originales de los objetivos (*targets*).
2. **Análisis Exploratorio (EDA):** Seguimiento de la concentración media de metales por etapa, comparación de distribuciones del tamaño de partícula (`rougher.input.feed_size`) entre entrenamiento y prueba, y eliminación coordinada de **2.296 registros anómalos** con concentraciones totales iguales a `0`.
3. **Modelado y Validación Cruzada:** Implementación de las métricas personalizadas **sMAPE** y **sMAPE final ponderado** ($0.25 \times \text{sMAPE}_{\text{rougher}} + 0.75 \times \text{sMAPE}_{\text{final}}$), evaluando **Regresión Lineal** y **Bosque Aleatorio** mediante validación cruzada de 5 pliegues (`KFold`).
4. **Evaluación Final:** Entrenamiento del modelo ganador sobre el conjunto de entrenamiento completo y evaluación final sobre el conjunto de prueba.

## 💡 Resultados y Métricas

### Evolución de Concentraciones Medias por Etapa
| Etapa del Proceso | Oro (Au) | Plata (Ag) | Plomo (Pb) |
| :--- | :---: | :---: | :---: |
| **Alimentación Inicial** | 7.55 | 8.04 | 3.28 |
| **Concentrado Rougher** | 17.83 | 10.85 | 6.89 |
| **Purificación Primaria** | 29.12 | 7.67 | 8.81 |
| **Concentrado Final** | **39.89** | 4.77 | 9.07 |

### Validación Cruzada (`KFold` - 5 Pliegues)
| Modelo Evaluado | Configuración / Hiperparámetros | sMAPE Promedio (Validación) |
| :--- | :--- | :---: |
| **Bosque Aleatorio (`RandomForestRegressor`)** | `n_estimators=10`, `max_depth=10` | **5.73%** |
| **Regresión Lineal (`LinearRegression`)** | Por defecto | 7.29% |

### 🏆 Evaluación Final en el Conjunto de Prueba
* **Modelo Seleccionado:** `RandomForestRegressor(n_estimators=10, max_depth=10, random_state=12345)`
* **sMAPE Ponderado Final en Prueba:** **7.39%**

## 📈 Principales Hallazgos
* **Enriquecimiento del mineral:** La concentración media de oro crece de forma continua desde **7.55** en la alimentación hasta **39.89** en el concentrado final, validando la eficiencia física de las etapas de flotación y purificación.
* **Superioridad de modelos no lineales:** En validación cruzada, el **Bosque Aleatorio** (`5.73%`) superó a la **Regresión Lineal** (`7.29%`) por **1.56 puntos porcentuales**, capturando mejor las interacciones complejas del proceso metalúrgico.
* **Estabilidad y generalización:** La cercanía entre el desempeño en validación cruzada (`5.73%`) y el conjunto de prueba (`7.39%`) confirma una adecuada capacidad de generalización sin sobreajuste (*overfitting*).

## 📂 Archivos Principales
* `Proyecto_Mu_Prediccion_de_Recuperacion_de_Oro.ipynb`
* `gold_recovery_full.7z`
* `gold_recovery_test.csv`
* `gold_recovery_train.7z`

## 🏁 Conclusión
Se desarrolló un sistema predictivo robusto basado en **Bosque Aleatorio** capaz de estimar la recuperación de oro en las etapas *rougher* y final con un **sMAPE ponderado de 7.39%** sobre datos no vistos. La solución proporciona a **Zyfra** una herramienta confiable para anticipar el rendimiento metalúrgico y optimizar las condiciones operativas en planta.
