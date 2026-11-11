# EDA - Generación y tratamiento de residuos en la Unión Europea

Proyecto de **Exploratory Data Analysis** centrado en la preparación y el análisis
exploratorio de datos de Eurostat sobre generación y tratamiento de
residuos en los países de la Unión Europea, junto con indicadores
económicos como el PIB per cápita y el peso de la industria en el PIB.

El trabajo se desarrolla en notebooks de Jupyter y se organiza en tres
bloques: limpieza de datos, preparación de indicadores económicos y
análisis exploratorio.

## Objetivos

- Describir la evolución de la generación de residuos, tanto en
  volumen total como por habitante.
- Comparar la generación y el tratamiento de residuos entre países.
- Analizar la proporción de residuos peligrosos y la composición por
  tipos y actividades.
- Calcular y comparar tasas de reciclaje.
- Contrastar si los países nórdicos de la UE (Dinamarca, Finlandia y Suecia) presentan tasas de
  reciclaje superiores a las del resto de países.
- Explorar la relación entre reciclaje, PIB per cápita y generación de
  residuos por habitante.
- Examinar la relación entre generación de residuos, PIB real y peso
  de la industria.

## Fuentes de datos

La fuente principal es **Eurostat**. Los conjuntos de datos utilizados
son:

| Código                                                                                          | Contenido                      | Uso                                                                                                       |
| ------------------------------------------------------------------------------------------------ | ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| [`env_wasgen`](https://ec.europa.eu/eurostat/databrowser/view/env_wasgen/default/table?lang=en) | Generación de residuos        | Limpieza y análisis de generación por país, año, peligrosidad, actividad (nace_r2) y tipo de residuo. |
| `env_wastrt`                                                                                   | Tratamiento de residuos        | Limpieza, tratamiento y análisis por país, año, peligrosidad, tratamiento y tipo de residuo.           |
| [`sdg_08_10`](https://ec.europa.eu/eurostat/databrowser/view/sdg_08_10/default/table?lang=en)   | PIB real per cápita           | Cruces con indicadores  de residuos                                                                       |
| `nama_10_a10`                                                                                  | Peso de la industria en el PIB |                                                                                                           |
| `nama_10_gdp`                                                                                  | PIB por país                  |                                                                                                           |

Los ficheros originales descargados de Eurostat son CSV comprimidos
(`.csv.gz`). Las extracciones personalizadas deben conservar sus filtros
y parámetros para facilitar su reproducción.

## Estructura del proyecto

Los principales notebooks de trabajo son:

```text
.
├── Limpieza de datos
├──── Limpieza_generacion.ipynb
├──── Limpieza_tratamiento.ipynb
├──── gdp.ipynb
├
├── EDA_generacion_residuos_UE.ipynb
├── EDA_reciclaje_residuos_UE.ipynb
├── Mapas.ipynb
└── README.md
```

Los notebooks de limpieza preparan las tablas que se utilizan en los
análisis exploratorios.

## Proceso de trabajo

### 1. Limpieza de generación

En `Limpieza_generacion.ipynb` se prepara el conjunto `env_wasgen`:

- Se eliminan campos administrativos que no se utilizan directamente
  en el análisis.
- Se renombran variables para facilitar su lectura.
- Se reorganizan las unidades mediante un pivotado para obtener
  medidas diferenciadas de `kg_per_capita` y `toneladas`.
- Se filtran las geografías de la UE y se excluyen categorías
  obsoletas identificadas como *up to 2008*.
- Se incorporan etiquetas en castellano manteniendo los códigos
  originales de Eurostat.
- Se exporta la tabla preparada como `gen_residuos.csv`.

### 2. Limpieza de tratamiento

En `Limpieza_tratamiento.ipynb` se aplica un proceso equivalente al
conjunto `env_wastrt`. Se conserva la variable de operación de
tratamiento (`wst_oper`), se reorganizan las unidades, se filtran
geografías y categorías y se traducen las etiquetas. El resultado se
exporta como `trt_residuos.csv`.

### 3. Preparación de indicadores económicos

En `gdp.ipynb` se preparan tablas de trabajo relacionadas con:

- PIB per cápita anual y su corte para 2024.
- Peso de la industria en el PIB.
- PIB real para el análisis de evolución temporal.

Entre los ficheros intermedios documentados en el proyecto se encuentran
`gdp_pc.csv`, `gdp_pc_2024.csv` y `gdp_industria.csv`.

### 4. Análisis exploratorio

Los notebooks `EDA_generacion_residuos_UE` y `EDA_reciclaje_residuos_UE`
reúnen el análisis descriptivo, las visualizaciones y los contrastes
estadísticos.

Se emplean, entre otras, las siguientes técnicas:

- Inspección de dimensiones, tipos de datos, valores ausentes y
  cobertura temporal y geográfica.
- Estadística descriptiva y análisis de distribuciones.
- Series temporales y comparaciones entre países.
- Gráficos de composición por tipo de residuo y actividad.
- Indicadores por habitante e índices temporales con base 100.
- Visualizaciones con Matplotlib, Seaborn y Plotly.
- Correlaciones de Pearson y Spearman.
- Prueba U de Mann--Whitney para comparar el grupo de países nórdicos
  con el resto.

## Definiciones de indicadores

**Generación por habitante:** se expresa en kg por habitante
(`kg_per_capita`) para facilitar la comparación entre países de distinto
tamaño poblacional.


**Proporción de residuos peligrosos:**

$$
\frac{\text{Toneladas de residuos peligrosos}}{\text{Toneladas totales de residuos}} \times 100
$$

**Tasa de reciclaje utilizada en la comparación de 2024:**

$$
\frac{\text{Toneladas de «Valorización - reciclado»}}{\text{Toneladas de «Tratamiento de residuos»}} \times 100
$$


Esta tasa representa la proporción reciclada sobre el total tratado, no
sobre el total generado.

## Resultados estadísticos destacados

Los resultados que están recogidos en los notebooks son:


| Análisis                                   | Resultado                                                                            | Interpretación                                                                                                         |
| :------------------------------------------ | :----------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------- |
| Países nórdicos frente al resto           | Mann–Whitney unilateral: $U = 16$; $p = 0,9331$                                 | No se obtiene evidencia estadística suficiente para afirmar que Dinamarca, Finlandia y Suecia tengan tasas superiores. |
| PIB per cápita y tasa de reciclaje         | Pearson $r = -0,145$ ($p = 0,4692$); Spearman $\rho = -0,091$ ($p = 0,6518$) | No se observa una asociación estadísticamente significativa.                                                          |
| Generación per cápita y tasa de reciclaje | Pearson $r = -0,620$ ($p = 0,0007$); Spearman $\rho = -0,535$ ($p = 0,0049$) | Se observa una asociación negativa y estadísticamente significativa en la comparación analizada.                     |

Los resultados describen asociaciones y diferencias en la muestra
utilizada; **no permiten establecer relaciones causales**. La
comparación del grupo nórdico debe interpretarse con cautela porque está
formado por tres países.

## Consideraciones y limitaciones

- La cobertura de países y años no es idéntica en todos los conjuntos
  de datos. En generación se registran 28 geografías entre 2004 y 2022
  y 27 en 2024.
- Los valores ausentes no deben interpretarse como ceros.
- Eurostat incluye indicadores de calidad y confidencialidad; conviene
  conservar campos como `OBS_FLAG` y `CONF_STATUS`.
- Algunos valores de 2024 relativos a residuos peligrosos y al total
  empleado en su cálculo aparecen identificados como imputados por
  Eurostat.
- En los análisis de composición se deben excluir los agregados
  generales y subtotales cuando se comparan sus componentes, para
  evitar doble conteo.
- Las extracciones personalizadas, especialmente la de PIB real,
  requieren conservar los parámetros de descarga para poder
  reproducirlas.
- Los análisis descriptivos deben interpretarse atendiendo a la
  definición exacta de cada indicador y a su denominador.

## Herramientas

- Python
- Jupyter Notebook
- pandas
- Matplotlib
- Seaborn
- Plotly
- SciPy (pruebas estadísticas)
- Folium

## Cómo reproducir el trabajo

1. Descarga desde Eurostat los conjuntos de datos indicados en la
   sección [Fuentes de datos](#fuentes-de-datos), conservando los
   filtros y parámetros de las extracciones.
2. Coloca los ficheros originales en las rutas de entrada utilizadas
   por los notebooks.
3. Ejecuta primero `Limpieza_generacion.ipynb` y
   `Limpieza_tratamiento.ipynb`.
4. Ejecuta `gdp.ipynb` para preparar los indicadores económicos.
5. Ejecuta los notebooks de análisis exploratorio de generación y
   reciclaje.

Los nombres y rutas de los ficheros de entrada deben coincidir con los
indicados en las celdas de carga de cada notebook. Se recomienda
ejecutar los notebooks de principio a fin para regenerar las tablas
intermedias y los resultados.

## Referencias

- Eurostat. *Generation of waste* (`env_wasgen`). [Data
  Browser](https://ec.europa.eu/eurostat/databrowser/view/env_wasgen/default/table?lang=en).
- Eurostat. *Treatment of waste* (`env_wastrt`). [Data
  Browser](https://ec.europa.eu/eurostat/databrowser/view/env_wastrt/default/table?lang=en).
- Eurostat. *GDP per capita* (`sdg_08_10`). [Data
  Browser](https://ec.europa.eu/eurostat/databrowser/view/sdg_08_10/default/table?lang=en).
- Eurostat. *National accounts aggregates by industry*
  (`nama_10_a10`). [Data
  Browser](https://ec.europa.eu/eurostat/databrowser/view/nama_10_a10/default/table?lang=en).
- Eurostat. *GDP and main components* (`nama_10_gdp`). [Data
  Browser](https://ec.europa.eu/eurostat/databrowser/view/nama_10_gdp/default/table?lang=en).

---

Proyecto académico de análisis de datos. La memoria complementaria
recoge con mayor detalle el proceso de preparación, las decisiones
metodológicas, las dificultades y la interpretación de resultados.
