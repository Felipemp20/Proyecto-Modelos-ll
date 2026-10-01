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
 
## Cómo reproducir los resultados
 
### 1. Descargar los datos
1. Crea una cuenta en [Kaggle](https://www.kaggle.com) e ingresa a la [competencia](https://www.kaggle.com/c/house-prices-advanced-regression-techniques/data). Si te lo pide, pulsa *Join Competition* y acepta las reglas.
2. Descarga `train.csv` y guárdalo en `data/train.csv`.
### 2a. Ejecutar localmente (Python 3.9 o superior)
```bash
git clone <URL-de-este-repositorio>
cd PrediCasa
python -m venv .venv
source .venv/bin/activate        # En Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook PrediCasa_EDA.ipynb
```
Luego ejecuta todas las celdas (*Run All*).
 
### 2b. Ejecutar en Google Colab (sin instalar nada)
1. En [colab.research.google.com](https://colab.research.google.com) elige *Archivo → Subir cuaderno* y sube `PrediCasa_EDA.ipynb`.
2. En el panel de archivos (ícono de carpeta) sube `train.csv`.
3. *Entorno de ejecución → Ejecutar todo*.
### Resultados generados
Al terminar, el notebook crea:
- `figures/01_saleprice_distribucion.png`
- `figures/02_nulos.png`
- `figures/03_correlacion_top15.png`
- `figures/04_heatmap_correlacion.png`
- `figures/05_variables_clave.png`
- `figures/06_precio_por_barrio.png`
- `data/train_preprocesado.csv` (1460 × 256), usado en la siguiente entrega.
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
## Resultados principales del EDA
 
- `SalePrice`: media USD 180 921, mediana USD 163 000, asimetría 1.88 → se modelará `log(1 + SalePrice)`.
- 19 columnas con nulos; en la mayoría `NA` significa que la vivienda no tiene la característica (piscina, callejón, garaje, sótano, etc.).
- Variables más correlacionadas con el precio: `OverallQual` (0.79), `GrLivArea` (0.71), `GarageCars` (0.64), `GarageArea` (0.62), `TotalBsmtSF` (0.61).
- Multicolinealidad (r > 0.8): `GarageCars`–`GarageArea`, `YearBuilt`–`GarageYrBlt`, `GrLivArea`–`TotRmsAbvGrd`, `TotalBsmtSF`–`1stFlrSF`.
- Dos valores atípicos (`Id` 524 y 1299) que se eliminarán antes del entrenamiento.
- Tras el preprocesamiento: 1460 × 256 (255 predictores) sin valores nulos.
## Próximos pasos
 
Entregable II: eliminar atípicos, dividir en entrenamiento/validación, comparar modelos lineales regularizados (Ridge, Lasso, Elastic Net) y de ensamble (Random Forest, Gradient Boosting, XGBoost) con validación cruzada y RMSLE.
 
## Referencias
 
- D. De Cock, "Ames, Iowa: Alternative to the Boston Housing Data as an End of Semester Regression Project", *Journal of Statistics Education*, 19(3), 2011.
- H. Sharma, H. Harsora y B. Ogunleye, "An Optimal House Price Prediction Algorithm: XGBoost", *Analytics*, 3(1), 30–45, 2024.
- P. Rath y R. Slater, "Predicting House Prices Using Machine Learning", *SMU Data Science Review*, 9(3), art. 12, 2025.
- K. P. Fourkiotis y A. Tsadiras, "Comparing Machine Learning Techniques for House Price Prediction", AIAI 2023, Springer, pp. 292–303.
- J. Wang et al., "Unveiling the Impact of Nonlinear Modeling in Housing Price Prediction: An Empirical Comparative Study", *Sci. J. Appl. Math. Stat.*, 14(1), 6–15, 2026.
## Licencia de los datos
 
Los datos pertenecen a Kaggle / sus autores y se rigen por las reglas de la competencia; por eso no se redistribuyen aquí.
