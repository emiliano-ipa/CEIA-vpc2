# CEIA – Visión por Computadora II | Proyecto Final

Repositorio del proyecto final de la materia **Visión por Computadora II** de la Carrera de Especialización en Inteligencia Artificial (CEIA – UBA).

---

## 👥 Integrantes

- **Martín Brocca** (<martinbrocca@gmail.com>)  
- **Emiliano Iparraguirre** (<emiliano.iparraguirre22@gmail.com>)  

---

## 🎯 Objetivo general

El proyecto busca analizar y comparar distintos enfoques de **clasificación de imágenes** sobre un mismo dataset, pasando desde modelos clásicos de *features ajustados manualmente* hasta arquitecturas modernas de **deep learning**.  

Los objetivos específicos fueron:  
1. Explorar el dataset y evaluar la calidad y el balance de clases.  
2. Entrenar un baseline clásico con descriptores de color y regresión logística.  
3. Desarrollar un baseline CNN con **ResNet18 congelada**.  
4. Mejorar el baseline aplicando **fine-tuning** y optimización de hiperparámetros con **Optuna**.  
5. Reestructurar el dataset en formato YOLO e implementar un modelo de clasificación con **YOLOv8**.  
6. Comparar las métricas finales de los diferentes enfoques.  

---

## 📂 Notebooks principales

### 1. `01_EDA.ipynb` y `01.1_balance_check.ipynb`  
**Objetivo:** 

Realizar un análisis exploratorio del dataset (EDA) y verificar el balance de clases.  

**Resultados esperados:**  
- Confirmar si el dataset estaba balanceado o no.
- Detectar clases con menor representación para prever dificultades en el entrenamiento.
- Anticipar que métricas como accuracy serían insuficientes y que sería necesario evaluar con **Macro-F1**.

**Conclusiones:**
- Se revisó la distribución de las categorías.  
- Se identificaron posibles sesgos y la necesidad de aplicar estrategias de balance.  
- El dataset presenta cierta desproporción entre clases, lo que motivó la evaluación con métricas robustas al desbalance como **F1-score** además del accuracy.  

---

### 2. `02_Baseline_and_Optimized.ipynb`  
**Objetivo:** 

Comparar distintos enfoques de clasificación para establecer una progresión desde un baseline simple hasta un modelo optimizado, y evaluar cómo el uso de **transfer learning** y la **optimización de hiperparámetros** impactan en la performance del modelo.

**Estrategia:**  
- **Baseline clásico:** extracción de histogramas de color y entrenamiento de una **regresión logística**.  
- **Baseline CNN:** uso de **ResNet18 pre-entrenada** con capas congeladas (solo se entrena el clasificador).  
- **Optimización:** liberación de la capa `layer4` para fine-tuning y búsqueda de hiperparámetros con **Optuna**.  

**Resultados esperados:**  
- Que el modelo clásico sirviera como punto de partida simple.  
- Que ResNet18 congelada mejorara significativamente el baseline clásico.  
- Que el fine-tuning + Optuna aportara la mejor performance dentro del enfoque CNN, alcanzando el mejor **equilibrio entre accuracy y F1**, con mejoras notorias en clases difíciles como trash.  

**Conclusiones:**  
- El baseline clásico logra resultados aceptables pero limitados.  
- ResNet18 congelada ya aporta una mejora clara.  
- El fine-tuning con Optuna optimiza aún más la capacidad de generalización del modelo.  

---

### 3. `03_YOLO.ipynb`  
**Objetivo:**  

Explorar un modelo distinto reestructurando el dataset en formato YOLO y evaluando **YOLOv8** para clasificación.  

**Justificación de la generación de datasets aparte:**  
- YOLO requiere un formato específico de organización de imágenes y etiquetas.  
- Se diseñó un script para exportar los splits ya definidos (train, val, test) al formato requerido.  

**Estrategia:**  
- Comparar el desempeño de YOLO respecto a los CNN entrenados en PyTorch.

**Resultados esperados:**  
- Que YOLOv8, como arquitectura de deep learning originalmente diseñada para detección, lograra resultados **competitivos o superiores** en clasificación respecto a los CNN entrenados en PyTorch.
- Que alcanzara **alta accuracy Top-1** y que la **accuracy Top-5** fuera cercana a 1.0, reflejando su capacidad de incluir la clase correcta dentro de las mejores predicciones incluso cuando no era la primera opción.

**Conclusiones:**
- YOLOv8, incluso en su versión ligera (`yolov8n-cls`), alcanzó un desempeño **muy superior** al de los modelos basados en CNN en términos de accuracy.  
- Logró un **Top-1 accuracy de 0.968** y un **Top-5 accuracy de 1.0**, lo que significa que en prácticamente todos los casos acierta la clase correcta como primera opción y siempre la incluye dentro de las cinco predicciones más probables.  
- Estos resultados confirman que YOLOv8 posee una **alta capacidad de generalización** y se posiciona como la arquitectura más robusta dentro de los enfoques evaluados en este proyecto.  

---

## 📊 Comparativo de resultados

### Métricas en el conjunto de **test**  

| Modelo / Estrategia                  | Accuracy | Macro-F1 | Comentarios clave |
|--------------------------------------|----------|----------|-------------------|
| ColorHist + Logistic Regression      | 0.567    | 0.556    | Baseline clásico con histogramas de color. Buen desempeño en *cardboard*, pero pobre en clases más difíciles (*glass*, *trash*). |
| ResNet18 (congelada, baseline CNN)   | 0.765    | 0.747    | Primer baseline con transfer learning congelado. Mejora sustancial frente al baseline clásico, con buenos resultados en *glass* y *metal*, aunque *trash* sigue siendo difícil. |
| ResNet18 (fine-tuning + Optuna)      | 0.868    | 0.855    | Fine-tuning de `layer4` con hiperparámetros optimizados vía Optuna. Logra el mejor Macro-F1 en test, con balance equitativo entre clases y mejoras notorias en *trash*. |
| **YOLOv8 (clasificación)**               | **0.968 (Top-1)** / **1.0 (Top-5)** | – | **En ~97% de los casos el modelo predice correctamente la clase en primer lugar. En el 100% de los casos, la clase verdadera aparece dentro de las 5 predicciones.** |

---

## 📝 Conclusiones finales

- El **baseline clásico** con histogramas de color y regresión logística resulta insuficiente para clases difíciles, aunque ofrece un punto de partida para el análisis.  
- La **ResNet18 congelada** mejora claramente los resultados, validando el aporte del *transfer learning* incluso con un entrenamiento mínimo.  
- El **fine-tuning de capas profundas con Optuna** obtiene el mejor **Macro-F1 (0.855)**, mostrando un balance más justo entre clases y un salto de calidad en la predicción de *trash*.  
- **YOLOv8**, una arquitectura de deep learning originalmente diseñada para detección de objetos y utilizada aquí en su variante de clasificación, supera a los anteriores en accuracy (Top-1: 0.968, Top-5: 1.0). El resultado implica que en casi todos los casos acierta la clase correcta como primera opción y siempre la incluye dentro de las cinco mejores predicciones.  
- En conjunto, se concluye que las estrategias de **transfer learning + optimización** y el uso de **arquitecturas avanzadas** como YOLO permiten alcanzar resultados de alta precisión y robustez en este problema de clasificación.  