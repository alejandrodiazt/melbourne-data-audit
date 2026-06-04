# Melbourne Housing Data Audit & Pipeline

Este repositorio contiene los módulos de desarrollo y análisis correspondientes a las fases de auditoría de calidad, depuración y modelado estructural de datos faltantes del dataset de bienes raíces de Melbourne (`melb_data.csv`). El proyecto se ha desarrollado bajo un enfoque cuantitativo, aislando sesgos metodológicos y estructurando soluciones óptimas para entornos de computación en la nube.

## Autoría
* **Ing. Mauricio Alejandro Díaz Tirado**

## Estructura del repositorio

```text
├── data/
│   └── melb_data.csv                  # Dataset original (13,580 registros, 21 columnas)
├── notebooks/
│   ├── limpieza_datos_BANCO.ipynb     # Fase 1: Limpieza de datos y detección de anomalías
│   └── datos_faltantes_BANCOS.ipynb   # Fase 2: Gestión y análisis de datos faltantes
└── README.md                          # Documentación técnica del proyecto
```

## Módulo 1: Limpieza de datos

El primer cuaderno ejecuta un proceso integral de ingeniería de la calidad de los datos, enfocado en garantizar la estabilidad estadística antes del modelado predictivo.

### Fases de ejecución y filtros aplicados

1. **Auditoría de dimensiones:** Inspección inicial del dataframe identificando una matriz base de $13,580 \times 21$.
2. **Tratamiento preliminar de vacíos (Listwise Deletion):** Eliminación estricta de registros con valores `NaN`. Esta operación reduce la dimensionalidad horizontal a $6,196$ registros limpios, actuando como un subset de control analítico.
3. **Análisis de cardinalidad catetórica:** Verificación de subniveles para variables de tipo objeto para descartar redundancia estructural (columnas con varianza cero o de un solo nivel).
   * `Suburb`: 287 subniveles.
   * `Type`: 3 subniveles.
   * `Method`: 5 subniveles.
   * `Regionname`: 8 subniveles.
4. **Detección de outliers y análisis de distribución:** Mediante el uso de diagramas de caja, se auditaron las variables cuantitativas críticas. Se identificaron asimetrías severas a la derecha en las variables de área de terreno y construcción, delimitando fronteras entre datos atípicos legítimos y potenciales errores de registro.

## Módulo 2: Manejo de datos faltantes

La segunda fase se enfoca en el diagnóstico profundo de los mecanismos de pérdida de datos utilizando la librería especializada `missingno`. 

### Diagnóstico cuantitativo del dataset original
* **Total de celdas:** $285,180$
* **Variables con datos faltantes críticos:**
  * `BuildingArea`: 6,450 valores faltantes (47.49% de la columna).
  * `YearBuilt`: 5,375 valores faltantes (39.58% de la columna).
  * `CouncilArea`: 1,369 valores faltantes (10.08% de la columna).

### Matriz de nulidad y mecanismos de pérdida
Se descubrió una correlación estructural severa en la ausencia simultánea de datos entre las variables `BuildingArea` y `YearBuilt`.

```text
                 Correlación de nulidad visualizada
BuildingArea   [████████████████░░░░░░░░░░░░████████████████] (Faltante)
                                 ||
YearBuilt      [████████████████░░░░░░░░░░░░████████████████] (Faltante)
```

#### Implicaciones teóricas
1. **Rechazo de MCAR (Missing Completely At Random):** La falta de datos no ocurre de manera aislada ni aleatoria. La coincidencia horizontal de nulidad demuestra dependencia estadística.
2. **Evidencia de mecanismo MAR / MNAR:** La ausencia simultánea responde a la naturaleza de la propiedad (terrenos no edificados) o a factores sistémicos (falta de digitalización de registros municipales en propiedades antiguas).
3. **Impacto en imputaciones:** La aplicación de imputaciones simples sesga la estructura de covarianza. La estrategia óptima requiere Imputación Multivariada por Ecuaciones Encadenadas (MICE) o algoritmos basados en vecindad (KNN Imputer), condicionando el año de construcción en función del área y de la localización geográfica.

## Instrucciones de reproducción

1. **Clonar el repositorio:**
   ```bash
   git clone [https://github.com/alejandrodiazt/melbourne-data-audit.git](https://github.com/alejandrodiazt/melbourne-data-audit.git)
   cd melbourne-data-audit
   ```

2. **Instalar dependencias necesarias:**
   ```bash
   pip install pandas notebook matplotlib seaborn missingno
   ```

3. **Ejecutar el entorno:**
   ```bash
   jupyter notebook
   ```