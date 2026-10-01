# PrediCasa – Predicción de precios de viviendas
 
Proyecto de aula de **Modelos y Simulación de Sistemas II**, Departamento de Ingeniería de Sistemas, Universidad de Antioquia.
 
**Equipo**
- Roller Andrés Hernández López
- Juan Miguel Cadena Zúñiga
- Juan Felipe Martínez Patiño
## Problema
 
Compradores, vendedores y agentes inmobiliarios suelen fijar el precio de una vivienda con base en su impresión o en comparaciones superficiales (metros cuadrados, número de habitaciones), sin considerar otras variables como la calidad de la construcción, la ubicación, la presencia de garaje o si la vivienda ha sido remodelada. Esto puede llevar a sobrevalorar o subvalorar propiedades.
 
**PrediCasa** busca estimar el precio de venta de una vivienda a partir de sus características usando aprendizaje de máquina. La configuración elegida es **aprendizaje supervisado de regresión**, evaluada con **RMSLE** (la métrica de la competencia), además de R² y MAE.
 
## Datos
 
- Fuente: [House Prices – Advanced Regression Techniques (Kaggle)](https://www.kaggle.com/c/house-prices-advanced-regression-techniques), basada en el conjunto *Ames Housing* (De Cock, 2011): ventas de viviendas en Ames, Iowa (EE. UU.), 2006–2010.
- `train.csv`: 1460 viviendas y 81 columnas (Id, 79 variables explicativas y `SalePrice`).
- **Los datos no se incluyen en este repositorio** (se descargan desde Kaggle, ver más abajo).
## Estructura del repositorio
 
```
PrediCasa/
├── PrediCasa_EDA.ipynb          # Entregable I: análisis exploratorio y preprocesamiento
├── reporte/
│   ├── PrediCasa_Entregable1.pdf  # Informe del Entregable I (formato IEEE)
│   ├── PrediCasa_Entregable1.tex  # Fuente LaTeX del informe
│   └── fig_corr.png               # Figura usada en el informe
├── data/                        # Aquí va train.csv (no versionado)
├── figures/                     # Figuras que genera el notebook
├── requirements.txt
└── README.md
```

## Contenido del notebook (Entregable I)
 
1. Carga de datos (tratando `NA` como ausencia de la característica donde corresponde).
2. Visión general del conjunto.
3. Distribución de la variable objetivo `SalePrice`.
4. Datos faltantes.
5. Correlaciones y multicolinealidad.
6. Relación de las variables clave con el precio (calidad, área, barrio, garaje, chimenea, remodelación).
7. Valores atípicos.
8. Preprocesamiento: imputación y codificación.
9. Definición de la aproximación de ML.
10. Conclusiones.

## Resultados principales
 
- `SalePrice`: media USD 180 921, mediana USD 163 000, asimetría 1.88 → se modelará `log(1 + SalePrice)`.
- 19 columnas con nulos; en la mayoría `NA` significa que la vivienda no tiene la característica (piscina, callejón, garaje, sótano, etc.).
- Variables más correlacionadas con el precio: `OverallQual` (0.79), `GrLivArea` (0.71), `GarageCars` (0.64), `GarageArea` (0.62), `TotalBsmtSF` (0.61).
- Multicolinealidad (r > 0.8): `GarageCars`–`GarageArea`, `YearBuilt`–`GarageYrBlt`, `GrLivArea`–`TotRmsAbvGrd`, `TotalBsmtSF`–`1stFlrSF`.
- Dos valores atípicos (`Id` 524 y 1299) que se eliminarán antes del entrenamiento.
- Tras el preprocesamiento: 1460 × 256 (255 predictores) sin valores nulos.
 
## Referencias
 
- D. De Cock, "Ames, Iowa: Alternative to the Boston Housing Data as an End of Semester Regression Project", *Journal of Statistics Education*, 19(3), 2011.
- H. Sharma, H. Harsora y B. Ogunleye, "An Optimal House Price Prediction Algorithm: XGBoost", *Analytics*, 3(1), 30–45, 2024.
- P. Rath y R. Slater, "Predicting House Prices Using Machine Learning", *SMU Data Science Review*, 9(3), art. 12, 2025.
- K. P. Fourkiotis y A. Tsadiras, "Comparing Machine Learning Techniques for House Price Prediction", AIAI 2023, Springer, pp. 292–303.
- J. Wang et al., "Unveiling the Impact of Nonlinear Modeling in Housing Price Prediction: An Empirical Comparative Study", *Sci. J. Appl. Math. Stat.*, 14(1), 6–15, 2026.
## Licencia de los datos
 
Los datos pertenecen a Kaggle / sus autores y se rigen por las reglas de la competencia; por eso no se redistribuyen aquí.
