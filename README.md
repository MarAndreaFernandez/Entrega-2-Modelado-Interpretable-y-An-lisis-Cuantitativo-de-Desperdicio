#Reto Frisa — Equipo 4 · Planta García
## Predicción y Optimización de Excesos en Manufactura de Anillos
 
**Tecnológico de Monterrey · Análisis de Ciencias de Datos · 2026**
 
---
 
## 👥 Integrantes
 
| Nombre | Matrícula | Modelo | Opción |
|---|---|---|---|
| Paola Mireles Ochoa | A01753439 | Regresión Lineal + LASSO | B — Brecha en Altura |
| Fernando Juárez Vargas | A00841666 | Regresión Lineal + Ridge | A — Exceso Real en Altura |
| Mar Andrea Fernández Higuera | A00227571 | Regresión Logística + Árbol de Decisión | C — Probabilidad de Defecto |
 
---
 
## Contexto del Reto
 
Frisa produce anillos y forjas de acero en la planta García. Para garantizar que cada pieza tenga suficiente material para el maquinado final, se configura un **exceso** en tres dimensiones: Diámetro Exterior (DE), Diámetro Interior (DI) y Altura. El problema: el exceso configurado actualmente es mayor al necesario en varias familias geométricas, generando desperdicio de material costoso.
 
**Pregunta central del reto:** ¿Qué exceso mínimo garantiza que la pieza siempre sea maquinable, sin desperdiciar material innecesario?
 
---
 
## 📁 Estructura del repositorio
 
```
reto-frisa-equipo4/
│
├── README.md
│
└── entrega_2_modelado/
    ├── Entrega2_ModeloA_FernandoJuarez.ipynb              ← Regresión Lineal + Ridge | Opción A
    ├── Entrega2_ModeloB_PaolaMireles.ipynb                ← Regresión Lineal + LASSO | Opción B
    └── Entrega2_ModeloC_MarFernandez.ipynb                ← Reg. Logística + Árbol   | Opción C
```
 
---
 
## 📊 Dataset
 
- **Fuente:** Base de datos de producción planta García — 6 meses
- **Registros:** 848 piezas (tras limpieza de duplicados)
- **Familias:** 14 familias geométricas — Casquillo 10 es la más numerosa (n=285, 33.6%)
- **Variables clave:** DE Final, DI Final, Altura Final, excesos configurados (DE/DI/Altura), excesos reales (2 mediciones por dimensión), Calidad, Motivo, Roladora, Familia geométrica
**Columnas de exceso real:**
```
DE Exceso Real 1 / DE Exceso Real 2
DI Exceso Real 1 / DI Exceso Real 2
Altura Exceso Real 1 / Altura Exceso Real 2
```
 
---
 
## ⚙️ Dependencias
 
Todos los notebooks corren en **Google Colab** sin instalación adicional. Las librerías utilizadas son parte del entorno estándar de Colab:
 
```python
# Manipulación de datos
pandas >= 1.5
numpy >= 1.23
 
# Visualización
matplotlib >= 3.6
seaborn >= 0.12
 
# Machine Learning
scikit-learn >= 1.2
 
# Estadística
scipy >= 1.9
```
 
Para verificar versiones en Colab:
```python
import pandas as pd, numpy as np, sklearn, scipy, matplotlib, seaborn
for lib in [pd, np, sklearn, scipy, matplotlib, seaborn]:
    print(lib.__name__, lib.__version__)
```
 
---
 
## Cómo reproducir el análisis
 
### Paso 1 — Preparar el archivo de datos
 
1. Descarga el archivo `Grupo202_Garcia_Base_de_Datos_6_meses_Rev_1.xlsx` (o `.csv`)
2. Súbelo a tu Google Drive en la ruta:
   ```
   Mi unidad / Frisa / Grupo202_Garcia-Base-de-Datos-6-meses-Rev-1_1_.csv
   ```
3. Si guardas el archivo en otra carpeta, actualiza la variable `RUTA` al inicio de cada notebook
### Paso 2 — Abrir el notebook en Google Colab
 
1. Ve a [colab.research.google.com](https://colab.research.google.com)
2. `Archivo → Abrir notebook → Google Drive` → selecciona el notebook
3. O abre directamente desde Drive haciendo clic derecho → `Abrir con → Google Colaboratory`
### Paso 3 — Ejecutar
 
```
Entorno de ejecución → Ejecutar todo  (Ctrl + F9)
```
 
Cuando aparezca el diálogo de montar Drive, haz clic en **Conectar con Google Drive** y autoriza el acceso. Los notebooks corren de principio a fin sin intervención adicional.
 
### Orden recomendado de ejecución
 
```
1. EDA_Casquillo10.ipynb               ← Contexto de la familia principal
2. Analisis_Exploratorio_Equipo4.ipynb ← EDA global (referencia para los modelos)
3. Entrega2_ModeloA_FernandoJuarez.ipynb
4. Entrega2_ModeloB_PaolaMireles.ipynb
5. Entrega2_ModeloC_MarFernandez.ipynb
```
 
Los modelos de Entrega 2 son independientes entre sí — pueden ejecutarse en cualquier orden.
 
---
 
## 🧠 Descripción de modelos
 
### Modelo A — Fernando Juárez Vargas
**Archivo:** `Entrega2_ModeloA_FernandoJuarez.ipynb`
 
**Opción A:** Predecir el exceso real físico en Altura como variable continua.
 
| | |
|---|---|
| **Variable objetivo** | `Exceso_Alt_Real` (mm) — exceso real promedio en Altura |
| **Población** | Familia Casquillo 10 (n=285) |
| **Modelo base** | Regresión Lineal Múltiple (OLS) |
| **Modelo adicional** | Ridge Regression (penalización L2) |
| **Predictores** | Exceso Configurado Altura, Exceso DE configurado, Altura Final, DE Final, Relación E/A, Roladora (dummy R5/R6) |
| **Métrica principal** | R² en test + R² CV 5-fold |
| **Hiperparámetro** | α de Ridge seleccionado por tabla de exploración (α=1.0 por defecto) |
 
**Hallazgo principal:** Ridge estabiliza los coeficientes respecto a OLS ante la multicolinealidad entre variables geométricas (DE, Altura, Relación E/A son proxies del tamaño). El Exceso Configurado en Altura es el predictor más relevante y estable en ambos modelos.
 
**Figuras generadas:**
- Correlaciones de predictores vs variable objetivo
- Gráfico de residuales (diagnóstico de homocedasticidad)
- Comparación OLS vs Ridge (coeficientes)
- Ridge path (trayectoria de coeficientes por α)
- Tabla comparativa de especificaciones alternativas
---
 
### Modelo B — Paola Mireles Ochoa
**Archivo:** `Entrega2_ModeloB_PaolaMireles.ipynb`
 
**Opción B:** Predecir la brecha entre exceso real y exceso configurado en Altura.
 
| | |
|---|---|
| **Variable objetivo** | `Brecha_Alt` = Exceso real Altura − Exceso configurado Altura (mm) |
| **Población** | Familia Casquillo 10 (n=285) |
| **Modelo base** | Regresión Lineal Múltiple (OLS) con p-valores e IC 95% calculados manualmente |
| **Modelo adicional** | LASSO Regression (penalización L1, selección automática de variables) |
| **Predictores** | Exceso Configurado Altura, Exceso DE configurado, Altura Final, DE Final, Relación E/A, Roladora (dummy) |
| **Métrica principal** | R² test + R² CV 5-fold + RMSE (mm) |
| **Hiperparámetro** | α de LASSO seleccionado por tabla de exploración (α=0.1 por defecto) |
 
**Hallazgo principal:** El Exceso Configurado en Altura tiene coeficiente β = −14.57 (p < 0.001, IC 95%: [−16.97, −12.18]): a mayor exceso configurado, mayor brecha positiva — el proceso sobreentrega más cuando se le pide más. LASSO confirma este predictor como el único robusto y lleva a cero los coeficientes de las demás variables.
 
**Diagnóstico Gauss-Markov incluido:**
- Test de Shapiro-Wilk (normalidad de residuos)
- Test de Pearson sobre |residuos| vs predichos (homocedasticidad)
- Estadístico Durbin-Watson (independencia)
- Distancia de Cook (influencia de observaciones)
---
 
### Modelo C — Mar Andrea Fernández Higuera
**Archivo:** `Entrega2_ModeloC_MarFernandez.ipynb`
 
**Opción C:** Predecir la probabilidad de defecto (clasificación binaria) sobre el dataset global.
 
| | |
|---|---|
| **Variable objetivo** | `Defecto` — 0=Pieza buena, 1=Defecto (reproceso o rechazo) |
| **Población** | Dataset global — todas las familias (n=848) |
| **Modelo base** | Regresión Logística (`class_weight='balanced'`, L2, C=1.0) |
| **Modelo adicional** | Árbol de Decisión (`class_weight='balanced'`, `max_depth` optimizado por CV) |
| **Predictores** | Exceso DE/DI/Alt Real (promedios), dummies de Familia geométrica, Relación E/A |
| **Métrica principal** | AUC-ROC (accuracy descartada por desbalanceo 95.6% / 4.4%) |
| **Validación** | StratifiedKFold 5-fold sobre train únicamente |
 
**Hallazgos principales:**
- `Exceso_DI_Real` es el predictor dominante en ambos modelos (β = −0.871, p < 0.01 en logística; primer split del árbol)
- Umbral operacional identificado por el árbol: **Exceso DI Real ≤ 17.5 mm** → tasa de defecto ~13.5% vs ~2.3% sobre el umbral
- `Exceso_DE_Real` no predice defectos significativamente (p = 0.43)
- Los excesos configurados predicen peor que los excesos reales (AUC-ROC ~0.62 vs ~0.74)
**Figuras generadas (fig_M1 a fig_M9):**
- Boxplots exceso real por calidad
- Matrices de confusión (logística y árbol)
- Coeficientes estandarizados
- Curvas ROC individuales y comparativa
- Árbol de decisión visualizado + reglas en texto
- Importancia de variables (Gini)
- Distribución de Exceso DI Real por zona de riesgo
---
 
## 📈 Resumen de métricas por modelo
 
| Modelo | Integrante | Métrica principal | Valor |
|---|---|---|---|
| Reg. Lineal (OLS) — Opción A | Fernando | R² test | ~0.15 |
| Ridge (α=1.0) — Opción A | Fernando | R² test / R² CV | ~0.15 / estable |
| Reg. Lineal (OLS) — Opción B | Paola | R² test / RMSE | ~0.38 / ~18 mm |
| LASSO (α=0.1) — Opción B | Paola | R² test / RMSE | ~0.37 / ~18 mm |
| Reg. Logística — Opción C | Mar | AUC-ROC test | ~0.743 |
| Árbol de Decisión — Opción C | Mar | AUC-ROC test | ~0.724 |
 
> Los valores exactos se actualizarán al ejecutar los notebooks con los datos reales.
 
---
 
## 🔑 Hallazgos transversales del equipo
 
1. **Exceso DI Real es la dimensión más crítica** — predictor dominante de defectos (Modelo C) y con la mayor variabilidad observada en el EDA global.
2. **El proceso sobreentrega sistemáticamente en Altura** — brecha media de +11 mm en Casquillo 10. El Exceso Configurado en Altura es el predictor más fuerte de la brecha (Modelos A y B).
3. **El exceso configurado predice peor el riesgo de defecto que el exceso real** — AUC-ROC de ~0.62 vs ~0.74 (Modelo C). El algoritmo actual de configuración no captura correctamente el riesgo.
4. **Umbral operacional concreto:** Piezas con Exceso DI Real < 17.5 mm tienen una tasa de defecto ~6× mayor que las que superan ese umbral.
5. **Casquillo 10 es la candidata más robusta para reducción de exceso** — n=285, tasa de defecto 3.5%, brecha en Altura sistemáticamente positiva, y evidencia estadística de sobreentrega en los tres modelos.
---
 
## ⚠️ Limitaciones importantes
 
- **Muestra de defectos pequeña:** Solo 37 defectos en 848 piezas. Las métricas del Modelo C tienen intervalos de confianza amplios.
- **Asociación, no causalidad:** Los coeficientes son relaciones observacionales. Confusores no medidos (grado de acero, operador, temperatura de lote) podrían explicar parte de las correlaciones.
- **Ventana temporal:** Los datos cubren 6 meses. Las conclusiones pueden no generalizarse a otros periodos o tras cambios de mantenimiento en las roladoras.
- **El Modelo C no recomienda reducir exceso en DI:** Por el contrario, confirma que DI es la dimensión de mayor riesgo. Cualquier reducción en DI requiere validación experimental previa.

 
