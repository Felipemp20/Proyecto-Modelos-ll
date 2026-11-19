# PrediCasa – Predicción de precios de viviendas

Proyecto de aula de **Modelos y Simulación de Sistemas II**, Departamento de Ingeniería de Sistemas, Universidad de Antioquia.

**Equipo**
- Roller Andrés Hernández López
- Juan Miguel Cadena Zúñiga
- Juan Felipe Martínez Patiño

## Problema

Compradores, vendedores y agentes inmobiliarios suelen fijar el precio de una vivienda con base en su impresión o en comparaciones superficiales (metros cuadrados, número de habitaciones), sin considerar otras variables como la calidad de la construcción, la ubicación, la presencia de garaje o si la vivienda ha sido remodelada. Esto puede llevar a sobrevalorar o subvalorar propiedades.

**PrediCasa** busca estimar el precio de venta de una vivienda a partir de sus características usando aprendizaje de máquina. La configuración elegida es **aprendizaje supervisado de regresión**, evaluada con **RMSLE** (la métrica de la competencia), además de RMSE, MAE (en dólares) y R².

## Datos

- Fuente: [House Prices – Advanced Regression Techniques (Kaggle)](https://www.kaggle.com/c/house-prices-advanced-regression-techniques), basada en el conjunto *Ames Housing* (De Cock, 2011): ventas de viviendas en Ames, Iowa (EE. UU.), 2006–2010.
- `data/train.csv`: 1460 viviendas y 81 columnas (Id, 79 variables explicativas y `SalePrice`).
- El archivo se incluye en el repositorio para que los notebooks se puedan ejecutar directamente (en Colab o en local). El `test.csv` de Kaggle no se usa porque no trae `SalePrice`; en su lugar se reserva un 20 % de `train.csv` como conjunto de prueba.

## Estructura del repositorio

```
Proyecto-Modelos-ll/
├── PrediCasa.ipynb                 # 1. Análisis exploratorio y preprocesamiento (Entregable I)
├── 02_modelos.ipynb                # 2. Entrenamiento y evaluación de los modelos (Entregable II)
├── 03_reduccion_dimension.ipynb    # 3. Análisis de variables, PCA y UMAP (Entregable II)
├── data/
│   └── train.csv
├── Figures/                        # Figuras del análisis exploratorio (Entregable I)
├── Reporte/
│   ├── PrediCasa.pdf               # Informe en formato IEEE
│   ├── PrediCasa.tex
│   └── Correlacion.png
├── requirements.txt
└── README.md
```

Al ejecutar los notebooks se crean dos carpetas más: `figures/`, con las gráficas de cada sección, y `results/`, con las tablas de resultados y las búsquedas en malla guardadas.

## Cómo ejecutar

Los notebooks se ejecutan **en orden**: primero `PrediCasa.ipynb`, luego `02_modelos.ipynb` y por último `03_reduccion_dimension.ipynb`. Cada uno es independiente (carga los datos y repite la preparación), así que también se pueden ejecutar por separado.

**En Google Colab**
1. *Archivo → Abrir cuaderno → GitHub* y pegar la URL de este repositorio.
2. Abrir el notebook y ejecutar *Entorno de ejecución → Ejecutar todas*.
3. La primera celda clona el repositorio automáticamente para leer `data/train.csv`. El notebook 03 instala `umap-learn` si no está disponible.

**En local**
```bash
git clone https://github.com/Felipemp20/Proyecto-Modelos-ll.git
cd Proyecto-Modelos-ll
pip install -r requirements.txt
jupyter notebook
```

**Tiempo aproximado** (Colab, CPU): `02_modelos.ipynb` entre 15 y 30 minutos (búsqueda en malla de seis modelos) y `03_reduccion_dimension.ipynb` unos 15 minutos (UMAP). Los resultados se guardan en `results/`; con `RECALCULAR = False`, una nueva ejecución carga los resultados guardados en lugar de volver a entrenar.

**Reproducibilidad:** todos los notebooks usan la semilla `SEED = 42` para la partición, los pliegues de validación cruzada y los modelos.

## Metodología

1. **Partición:** se eliminan los dos valores atípicos (`Id` 524 y 1299) y se separa un 80 % para entrenamiento (1166 viviendas) y un 20 % para prueba (292), estratificando por deciles del precio.
2. **Preprocesamiento dentro de un `Pipeline`:** imputación según el significado de `NA`, codificación ordinal de las variables de calidad, one-hot de las nominales y estandarización de las numéricas. Todo se ajusta solo con los datos de entrenamiento de cada pliegue para evitar fuga de información.
3. **Variable objetivo:** los modelos se entrenan con `log(1 + SalePrice)` y las predicciones se devuelven en dólares para calcular RMSE y MAE.
4. **Validación:** búsqueda en malla de hiperparámetros con validación cruzada de 5 pliegues. Intervalos de confianza del 95 % con la t de Student (entrenamiento y validación) y bootstrap (prueba).
5. **Modelos:** ElasticNet (paramétrico), árbol de decisión (no paramétrico), Random Forest y XGBoost (ensambles), red neuronal MLP y SVR con kernel RBF.
6. **Reducción de dimensión** sobre los dos mejores modelos: análisis individual de variables, PCA (lineal) y UMAP (no lineal).

## Resultados principales

**Entregable I – Análisis exploratorio**
- `SalePrice`: media USD 180 921, mediana USD 163 000, asimetría 1.88 → se modela `log(1 + SalePrice)`.
- 19 columnas con nulos; en la mayoría `NA` significa que la vivienda no tiene la característica (piscina, callejón, garaje, sótano, etc.).
- Variables más correlacionadas con el precio: `OverallQual` (0.79), `GrLivArea` (0.71), `GarageCars` (0.64), `GarageArea` (0.62), `TotalBsmtSF` (0.61).
- Multicolinealidad (r > 0.8): `GarageCars`–`GarageArea`, `YearBuilt`–`GarageYrBlt`, `GrLivArea`–`TotRmsAbvGrd`, `TotalBsmtSF`–`1stFlrSF`.

**Entregable II – Modelos** (RMSLE, menor es mejor; ± intervalo de confianza del 95 %)

| Modelo | Validación | Prueba | R² prueba |
|---|---|---|---|
| SVR (RBF) | 0.115 ± 0.021 | 0.111 | 0.937 |
| ElasticNet | 0.115 ± 0.016 | 0.110 | 0.934 |
| Red neuronal (MLP) | 0.118 ± 0.022 | 0.115 | 0.925 |
| XGBoost | 0.122 ± 0.011 | 0.106 | 0.938 |
| Random Forest | 0.136 ± 0.022 | 0.125 | 0.912 |
| Árbol de decisión | 0.194 ± 0.016 | 0.164 | 0.849 |

Los dos mejores modelos por validación son **SVR** y **ElasticNet**.

**Entregable II – Reducción de dimensión** (RMSLE de validación)

| Variante | SVR | ElasticNet | Reducción de dimensiones |
|---|---|---|---|
| Sin reducción | 0.115 | 0.115 | — |
| Sin las 21 variables candidatas | 0.114 | 0.115 | ≈ 16 % |
| PCA (95 % de varianza) | 0.119 | 0.121 | ≈ 72 % |
| UMAP | 0.188 | 0.229 | ≈ 97 % |

PCA reduce cerca de tres cuartas partes de las dimensiones con una pérdida de desempeño dentro de los intervalos de confianza; UMAP reduce más pero pierde la información relevante para el precio.

## Referencias

- D. De Cock, "Ames, Iowa: Alternative to the Boston Housing Data as an End of Semester Regression Project", *Journal of Statistics Education*, 19(3), 2011.
- H. Sharma, H. Harsora y B. Ogunleye, "An Optimal House Price Prediction Algorithm: XGBoost", *Analytics*, 3(1), 30–45, 2024.
- P. Rath y R. Slater, "Predicting House Prices Using Machine Learning", *SMU Data Science Review*, 9(3), art. 12, 2025.
- K. P. Fourkiotis y A. Tsadiras, "Comparing Machine Learning Techniques for House Price Prediction", AIAI 2023, Springer, pp. 292–303.
- J. Wang et al., "Unveiling the Impact of Nonlinear Modeling in Housing Price Prediction: An Empirical Comparative Study", *Sci. J. Appl. Math. Stat.*, 14(1), 6–15, 2026.
